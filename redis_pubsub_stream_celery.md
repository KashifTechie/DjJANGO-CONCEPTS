# Redis, Streams, Pub/Sub, Rate-Limiting — and **complete Celery** config for Django

Concise, practical, and configuration-focused. Starts with **what Redis is**, then install on Ubuntu, then Pub/Sub + Streams + Rate-Limiting (with commands + Python examples), and finishes with a **complete Celery** setup (settings, `celery.py`, `apps.py`, tasks, run commands) and a brief explanation of each config piece.

---

# What **Redis** is (deep, but short)

* **Redis** = *in-memory data platform* (key → value) **with rich data structures** (Strings, Lists, Hashes, Sets, Sorted Sets, Streams, Bitmaps, HyperLogLog, Geo).
* **Primary reasons to use Redis**

  * **Speed**: data in RAM → microsecond reads/writes.
  * **Flexible primitives**: build caches, queues, counters, leaderboards, sets, rate-limiters without external services.
  * **Pub/Sub & Streams**: real-time messaging (Pub/Sub ephemeral; Streams persistent log-like).
  * **Atomic ops & concurrency primitives**: INCR, SETNX, Lua scripts → safe multi-client coordination.
  * **Optional persistence**: RDB/AOF (so Redis can act like a fast DB when configured).
* **When Redis is appropriate**: caching, session store, counters, short-lived queues, real-time features, fast lookups, lightweight stream/eventing. Not a replacement for relational DB for complex queries.

---

# Install Redis on Ubuntu (quick commands + explanation)

```bash
# Install
sudo apt update
sudo apt install -y redis-server

# Enable/Start
sudo systemctl enable redis-server
sudo systemctl start redis-server

# Check status
sudo systemctl status redis-server
# Test with client
redis-cli ping    # should return PONG
```

**Notes**

* Config file: `/etc/redis/redis.conf`

  * `supervised systemd` → systemd integration.
  * `bind` → which interfaces to listen on.
  * `protected-mode` → security for exposed instances.
* Persistence: enable/configure `save` (RDB) lines and `appendonly yes` for AOF in `redis.conf`.
* Secure production: bind to private network, requirepass or ACLs, TLS (newer Redis versions), and run behind firewall.

---

# Client libraries to install (Python/Django)

```bash
pip install redis django-redis celery
```

* `redis` = low-level Python client
* `django-redis` = Django cache backend integration
* `celery` = task queue (we’ll configure it later)

---

# Redis core pieces we will use (quick map)

* **Key operations**: `SET`, `GET`, `DEL`, `EXPIRE`, `TTL`
* **List (queue)**: `LPUSH`, `RPUSH`, `LPOP`, `RPOP`, `BRPOP`
* **Hash**: `HSET`, `HGET`, `HGETALL`
* **Pub/Sub**: `PUBLISH`, `SUBSCRIBE`, `PSUBSCRIBE`
* **Streams**: `XADD`, `XREAD`, `XREADGROUP`, `XGROUP`, `XACK`, `XPENDING`, `XINFO`
* **Atomic counters/locks**: `INCR`, `SETNX` (or `SET key value NX PX 1000`), Lua scripting for complex atomic ops

---

# PUB/SUB — ephemeral, push-based messaging

### Characteristics (short)

* **Push**: server pushes messages to currently subscribed clients.
* **Ephemeral**: messages are **not stored**; offline subscribers miss messages.
* **Use**: real-time notifications, live updates, chat presence.

### Core commands

* `PUBLISH channel message`
* `SUBSCRIBE channel [channel...]`
* `PSUBSCRIBE pattern`
* `UNSUBSCRIBE`, `PUNSUBSCRIBE`

### Python example (redis-py)

```python
import redis, threading

r = redis.Redis()

# Publisher
r.publish('news', 'Breaking: Redis Pub/Sub works!')

# Subscriber (blocking loop)
def listen():
    sub = r.pubsub()
    sub.subscribe('news')
    for msg in sub.listen():
        # msg = {'type': 'message', 'pattern': None, 'channel': b'news', 'data': b'...'}
        if msg['type'] == 'message':
            print('Got:', msg['data'].decode())

t = threading.Thread(target=listen, daemon=True)
t.start()
```

**When not to use Pub/Sub**

* If you need **durability**, replay, or guaranteed delivery → use **Streams** or Kafka.

---

# STREAMS — persistent, log-like, consumer groups

### Characteristics

* **Append-only** log per stream (`XADD`) stored on disk if persistence enabled.
* **Consumer groups** allow multiple consumers to divide work and track offsets.
* **Replayable**: consumers can re-read from older IDs.
* Good for event sourcing, durable queues, complex consumer semantics.

### Core commands (quick)

* `XADD mystream * field1 val1 field2 val2` — append event, returns ID `1588888888888-0`
* `XREAD COUNT 10 BLOCK 0 STREAMS mystream $` — read new messages
* `XGROUP CREATE mystream mygroup $` — create consumer group
* `XREADGROUP GROUP mygroup consumer COUNT 10 BLOCK 0 STREAMS mystream >` — read as consumer
* `XACK mystream mygroup <id>` — acknowledge processed entries
* `XPENDING mystream mygroup` — check pending messages
* `XTRIM mystream MAXLEN ~ 10000` — trim stream

### Python example (basic producer & consumer group)

```python
r = redis.Redis()

# producer: add messages
r.xadd('orders', {'user_id': '42', 'amount': '9.99'})

# setup consumer group (once)
try:
    r.xgroup_create('orders', 'workers', id='$', mkstream=True)
except redis.ResponseError:
    pass  # group exists

# consumer loop
while True:
    entries = r.xreadgroup('workers', 'consumer-1', {'orders': '>'}, count=10, block=5000)
    if not entries:
        continue
    for stream_name, messages in entries:
        for message_id, fields in messages:
            # process
            process(fields)
            r.xack('orders', 'workers', message_id)
```

**Notes**

* Use `XGROUP SETID` & `XPENDING` for recovery and dead-letter handling.
* Streams are the recommended Redis primitive for production durable messaging.

---

# Rate-limiting patterns (simple + robust)

## 1) Fixed window counter (simple)

```python
# allow N requests per WINDOW seconds per key
def allow_request(r, key, limit=100, window=60):
    p = r.pipeline()
    p.incr(key)
    p.expire(key, window)
    count, _ = p.execute()
    return count <= limit
```

* Pros: simple.
* Cons: burst at window boundaries.

## 2) Sliding window using sorted set (more accurate)

```python
import time
def allow_sliding(r, key, limit=100, window=60):
    now = time.time()
    min_score = now - window
    zkey = f"rl:{key}"
    p = r.pipeline()
    p.zremrangebyscore(zkey, 0, min_score)
    p.zadd(zkey, {str(now): now})
    p.zcard(zkey)
    p.expire(zkey, window)
    _, _, count, _ = p.execute()
    return count <= limit
```

* Keeps timestamps in a ZSET; removes old entries; count is active requests.

## 3) Token bucket via Lua (production-grade)

* Implement with a Lua script to atomically refill tokens and take one — prevents race conditions and is efficient. (Omitted the full script here; ask if you want it.)

---

# Message queue in Redis: Lists vs Streams — quick guidance

* **Lists (LPUSH / BRPOP)**: simple queue, good for quick worker queues (Celery default historically). Messages are in RAM and may be lost unless persistence configured.
* **Streams**: durable, consumer groups, replay, better for complex distributed consumers. Prefer Streams for reliable processing.

---

# CELERY — start → why → architecture → full Django configuration

## What is Celery? (very short)

* **Celery** = distributed task queue for running background jobs (send emails, process images, long CPU or IO work).
* It’s **not** a broker; it requires a broker (Redis, RabbitMQ, SQS) to broker tasks and optionally a result backend.

## Why use Celery?

* Offload long tasks from web request path → fast responses.
* Scale background processing horizontally.
* Schedule periodic tasks (Celery Beat).
* Retry, task acknowledgment, timeouts, chained workflows.

## Celery architecture (simple)

* **Producer**: your Django app enqueues tasks (`task.delay()`).
* **Broker**: Redis/RabbitMQ stores tasks until workers pull them.
* **Worker**: one or many processes that execute tasks.
* **Result backend**: optional store for task results (Redis, DB).
* **Beat**: scheduler to enqueue periodic tasks.

---

## Full Django + Celery setup (complete, explained)

### 1) pip install

```bash
pip install celery redis django-redis
```

### 2) `project/celery.py` (standard pattern)

```python
# project/celery.py
from __future__ import annotations
import os
from celery import Celery

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'project.settings')

app = Celery('project')

# Load config from Django settings with namespace CELERY_
# e.g., CELERY_BROKER_URL in settings maps to app.conf.broker_url
app.config_from_object('django.conf:settings', namespace='CELERY')

# Auto-discover tasks.py in installed apps
app.autodiscover_tasks()

# optional: for nicer logging
@app.task(bind=True)
def debug_task(self):
    print(f'Request: {self.request!r}')
```

**What each bit does**

* `os.environ.setdefault(...)` → ensures Django settings available for tasks.
* `Celery('project')` → create app instance (name used in logs/stats).
* `config_from_object(..., namespace='CELERY')` → load settings prefixed with `CELERY_`.
* `autodiscover_tasks()` → finds `tasks.py` in each installed app.

### 3) `project/__init__.py` (so Django imports celery on startup)

```python
from .celery import app as celery_app

__all__ = ('celery_app',)
```

* Ensures `celery_app` is importable with Django.

### 4) `project/settings.py` (important Celery settings — full + explanation)

```python
# Broker & Result backend
CELERY_BROKER_URL = 'redis://localhost:6379/0'      # broker: where tasks are sent
CELERY_RESULT_BACKEND = 'redis://localhost:6379/1'  # where results are stored (optional)

# Serialization
CELERY_TASK_SERIALIZER = 'json'
CELERY_RESULT_SERIALIZER = 'json'
CELERY_ACCEPT_CONTENT = ['json']  # secure: only accept json payloads

# Timezone
CELERY_TIMEZONE = 'UTC'
CELERY_ENABLE_UTC = True

# Worker behaviour
CELERYD_PREFETCH_MULTIPLIER = 1   # tasks a worker prefetches (1 → safer for time-consuming tasks)
CELERY_ACKS_LATE = True           # ack after successful run (so tasks requeued on failure)
CELERYD_CONCURRENCY = 4          # number of worker processes (or use -c on CLI)
CELERY_TASK_SOFT_TIME_LIMIT = 300 # seconds (soft kill)
CELERY_TASK_TIME_LIMIT = 400      # hard kill

# Reliability & visibility
CELERY_TASK_IGNORE_RESULT = False
CELERY_TASK_TRACK_STARTED = True
CELERY_RESULT_EXPIRES = 3600      # seconds before result backend expires entry

# Broker options (Redis specific)
CELERY_BROKER_TRANSPORT_OPTIONS = {
    'visibility_timeout': 3600,  # for Redis broker: time a task is reserved if worker dies
    # 'max_connections': 10
}

# Optional: beat schedule (periodic tasks)
from celery.schedules import crontab
CELERY_BEAT_SCHEDULE = {
    'cleanup-task-every-midnight': {
        'task': 'app.tasks.cleanup',
        'schedule': crontab(hour=0, minute=0),
    },
}

# Optional: task routes (route tasks to named queues)
CELERY_TASK_ROUTES = {
    'app.tasks.heavy_task': {'queue': 'heavy'},
}

# Optional: task annotations (rate limit)
CELERY_TASK_ANNOTATIONS = {
    'app.tasks.send_email': {'rate_limit': '10/m'}  # limit to 10 per minute per worker
}
```

**Explaination of key options**

* `CELERY_BROKER_URL` → broker connection (Redis in our examples).
* `CELERY_RESULT_BACKEND` → where results/status stored.
* `TASK_SERIALIZER/ACCEPT_CONTENT` → security & format of payloads.
* `TIME_LIMITS` → soft & hard kill to avoid runaway tasks.
* `PREFETCH_MULTIPLIER` & `CONCURRENCY` → control throughput and fairness.
* `ACKS_LATE` → ensures retry if worker crashes before finishing task.
* `VISIBILITY_TIMEOUT` → how long a task is invisible to other workers after being reserved; critical for Redis broker.
* `BEAT_SCHEDULE` → periodic tasks config (like cron).

### 5) `app/apps.py` — optional auto-start Celery on Django ready (not required but common pattern)

```python
# app/apps.py
from django.apps import AppConfig

class AppConfig(AppConfig):
    name = 'app'

    def ready(self):
        # Import signals/tasks if needed; ensures tasks modules loaded
        import app.signals  # noqa
```

* Use `ready()` to import modules that register tasks/signals.

### 6) Creating tasks — `app/tasks.py`

```python
from celery import shared_task
from time import sleep

@shared_task(bind=True)
def add(self, x, y):
    return x + y

@shared_task(bind=True, autoretry_for=(Exception,), retry_backoff=True, max_retries=3)
def flaky_task(self, data):
    # retries automatically on exceptions
    process(data)

@shared_task(rate_limit='10/m')
def send_email(user_id):
    # send email code
    pass
```

* `@shared_task` → task usable without import of Celery app.
* `bind=True` → `self` available for context (request, retries, id).
* `autoretry_for`/`retry_backoff` → automatic retry configuration.

### 7) Running Celery (CLI)

```bash
# start worker
celery -A project worker --loglevel=info --concurrency=4 -Q default,heavy

# start beat (scheduler)
celery -A project beat --loglevel=info

# or run both (development) via:
celery -A project worker -B --loglevel=info
```

**Explain CLI flags**

* `-A project` → load Celery app in `project.celery`
* `--concurrency` → number of processes/threads (controlled by worker pool)
* `-Q` → which queues to listen to
* `-B` → run beat inside worker (not recommended for production)

### 8) Production deployment tips

* Run workers under systemd, supervisord, or container orchestration (K8s).
* Use separate queues for long/short tasks; route heavy tasks to dedicated worker pools.
* Monitor worker health and broker metrics (Redis memory, latency).
* Configure `broker_pool_limit`, `broker_heartbeat`, `result_backend_transport_options` as needed.

---

# Celery + Redis specifics (common gotchas)

* **Redis as broker**: Redis does not support visibility timeout natively cleanly like RabbitMQ. Use `visibility_timeout` in `BROKER_TRANSPORT_OPTIONS` to avoid double processing if worker crashes. Use Streams or RabbitMQ for critical exactly-once semantics.
* **Result backend expiry**: `CELERY_RESULT_EXPIRES` controls memory/disk growth in result backend.
* **Prefetch & acks**: `worker_prefetch_multiplier` and `acks_late` interplay affects reliability vs throughput.
* **Serialization**: avoid `pickle` in untrusted environments — prefer `json`.

---

# Examples: Celery + Django + Redis full minimal settings snippets

`settings.py`

```python
INSTALLED_APPS = [
    # ...
    'django_celery_results',  # optional: stores results in DB
    'app',
]

# Redis + Celery config (as above)
CELERY_BROKER_URL = 'redis://localhost:6379/0'
CELERY_RESULT_BACKEND = 'redis://localhost:6379/1'

# Optional: django-celery-results
CELERY_RESULT_BACKEND = 'django-db'  # if using DB for results
```

Install `django-celery-results` if you want results stored in Django DB:

```bash
pip install django-celery-results
# add 'django_celery_results' to INSTALLED_APPS and run migrations
python manage.py migrate django_celery_results
```

---

# Quick checklist when moving to production

* Secure Redis (ACLs/TLS, private network).
* Use separate Redis DB indices for broker vs cache vs results.
* Use monitoring (Prometheus exporters, Celery flower).
* Use proper process manager for Celery (systemd, supervisord).
* Prefer Streams or RabbitMQ for mission-critical task durability.

---

# Left-out usages you asked to include (concise)

* **Streaming** → use Redis Streams (`XADD`, consumer groups) for durable event log, replay, multiple consumers with pending entry list & ack. Great for event-sourcing and pipelines.
* **Message Queue** → two choices in Redis:

  * **Lists** (`LPUSH/BRPOP`) for simple FIFO queues (fast, simple, ephemeral).
  * **Streams** for persistent, consumer-group semantics (recommended for production when you need replay/ack).

---

# Quick cheat-sheet: Pub/Sub & Streams commands (compact)

**Pub/Sub**

* `PUBLISH channel message`
* `SUBSCRIBE channel`
* `PSUBSCRIBE pattern`
* `UNSUBSCRIBE`

**Streams**

* `XADD stream * field value`
* `XREAD [COUNT n] [BLOCK ms] STREAMS stream id`
* `XGROUP CREATE stream groupname id`
* `XREADGROUP GROUP groupname consumer STREAMS stream >`
* `XACK stream groupname id`
* `XPENDING stream groupname`
* `XTRIM stream MAXLEN ~ N`
* `XINFO STREAM stream` / `XINFO GROUPS stream`

---

# Final notes & TL;DR

* **Redis** is a versatile in-memory engine; **how** you use its features determines whether it behaves as cache, queue, stream, or lightweight DB.
* **Pub/Sub** is ephemeral, immediate, not replayable. **Streams** are persistent & replayable with consumer groups.
* **Rate limit** with `INCR+EXPIRE` for simple needs; use ZSET sliding window or Lua token bucket for robustness.
* **Celery**: set broker (Redis), result backend, proper timeouts, prefetch/ack settings, and run workers; prefer Streams or RabbitMQ for mission-critical task durability if Redis broker limitations concern you.

---

If you want, I can now:

* Generate **ready-to-copy `settings.py`, `celery.py`, `Procfile` and systemd unit** for Celery.
* Provide a **Lua token-bucket script** for production rate limiting.
* Provide **example docker-compose** that runs Django + Redis + Celery worker + Celery beat + Flower.

Which of those should I produce next?
