# Django + Redis Task Tools: Beginner Study Guide

This document introduces three essential tools for using Redis with Django for task management and caching:

1. **django\_rq** – Lightweight job queue
2. **Celery + Redis** – Full-featured distributed task queue
3. **django-redis** – Redis-based caching system

Each section explains the purpose, installation, configuration, usage, and common functions with examples.

---

## 1. django\_rq

### 🔷 What is it?

`django_rq` is a Django wrapper for [RQ (Redis Queue)](https://python-rq.org/), a simple Python library for queueing jobs and processing them in the background with workers. It integrates with Django admin for job monitoring.

### 🔧 Installation

```bash
pip install django_rq
```

### ⚙️ Configuration in `settings.py`

```python
INSTALLED_APPS = [
    ...,
    'django_rq',
]

RQ_QUEUES = {
    'default': {
        'HOST': 'localhost',
        'PORT': 6379,
        'DB': 0,
        'DEFAULT_TIMEOUT': 360,
    }
}
```

Add to `urls.py`:

```python
from django.urls import path, include

urlpatterns = [
    ...,
    path('django-rq/', include('django_rq.urls')),
]
```

### ✅ Usage

**tasks.py**

```python
import time

def say_hello(name):
    time.sleep(3)
    print(f"Hello, {name}!")
```

**views.py**

```python
import django_rq
from myapp.tasks import say_hello

queue = django_rq.get_queue()
queue.enqueue(say_hello, 'Alice')
```

### 🔧 Useful Functions with Examples

These helper functions are part of `django_rq` and provide direct access to the core components involved in job queuing and scheduling. Here's what each one does and when you should use them:

Each of the following examples demonstrates key functionality using:

* `get_queue()` – for enqueueing tasks
* `get_connection()` – for direct Redis manipulation
* `get_worker()` – for inspecting worker state
* `get_scheduler()` – for scheduled jobs

```python
from django_rq import get_queue, get_connection, get_worker, get_scheduler

# get_queue():
# Use this to enqueue jobs into a Redis queue. This is essential to dispatch background tasks.
queue = get_queue("default")

# get_connection():
# Use this to access the raw Redis connection directly — for custom operations like setting, getting,
# or incrementing Redis keys unrelated to task processing.
conn = get_connection("default")

# get_worker():
# Use this to inspect or manage the current worker (e.g., get its status or job in progress).
worker = get_worker("default")

# get_scheduler():
# Use this to schedule jobs to be run at a specific time or interval (like a cron job).
scheduler = get_scheduler("default")

queue = get_queue("default")            # Get queue instance
conn = get_connection("default")        # Get raw Redis connection
worker = get_worker("default")          # Get worker for the queue
scheduler = get_scheduler("default")    # Get scheduler for delayed jobs
```

**Queue Example**

```python
# Enqueue a task to run immediately
queue = get_queue("default")
queue.enqueue(say_hello, 'Enqueued Now')
```

### 🔍 How This Works in Detail

1. **Enqueueing**:

   * `queue.enqueue(...)` pushes a job into a Redis list under the `rq:queue:default` key.
   * The job is serialized (function path, args, kwargs) and given a unique job ID.

2. **Storage**:

   * RQ stores the job data in Redis hashes (like `rq:job:<job_id>`) and the queue name holds a list of job IDs waiting to be processed.

3. **Execution**:

   * A separate RQ **worker** process (started using `python manage.py rqworker`) constantly polls the Redis queue.
   * When it finds a job, it pulls the job ID, fetches the job data, and executes the task function (e.g., `say_hello('Enqueued Now')`).

4. **Completion**:

   * Once executed, the job’s result, status (`finished`, `failed`, etc.), and execution metadata are saved in Redis for tracking.

5. **Monitoring**:

   * You can view job history in the Django Admin at `/django-rq/`.

### 🧠 Notes

* If no worker is running, the job stays in Redis but is never executed.
* Multiple workers can consume from the same queue for concurrency.
* Delayed jobs should use `get_scheduler().enqueue_in(...)`.

````

**Connection Example**
```python
conn = get_connection("default")

# Set and retrieve a key
conn.set("sample_key", "hello")
print(conn.get("sample_key"))  # Output: b'hello'

# Increment and decrement values
conn.incr("my_counter")
conn.incr("my_counter")
conn.decr("my_counter")
print(conn.get("my_counter"))  # Output: b'1'
````

**Worker Example**

```python
worker = get_worker("default")

# Print basic worker details
print("Worker Name:", worker.name)
print("Worker State:", worker.state)

# Access current job if any
if worker.get_current_job():
    print("Current Job:", worker.get_current_job().id)
else:
    print("No active job")
```

**Scheduler Example**

```python
from datetime import timedelta
scheduler = get_scheduler("default")

# Schedule a task to run in 10 seconds
scheduler.enqueue_in(timedelta(seconds=10), say_hello, 'Scheduled Alice')

# Schedule a recurring task every minute
scheduler.schedule(
    scheduled_time=timedelta(seconds=30),
    func=say_hello,
    args=["Recurring Bob"],
    interval=60,   # repeat every 60 seconds
    repeat=3       # run 3 times total
)
```

### 👨‍💼 Admin Interface

* Go to `/django-rq/` in the browser.
* Monitor job status, retry, or delete jobs.

---

## 2. Celery + Redis

### 🔷 What is it?

[Celery](https://docs.celeryq.dev/en/stable/) is an advanced, distributed task queue system for Python. It supports task retrying, scheduling, prioritization, and chaining.

### 🔧 Installation

```bash
pip install celery redis
```

### ⚙️ Configuration in `settings.py`

```python
CELERY_BROKER_URL = 'redis://localhost:6379/0'
CELERY_RESULT_BACKEND = 'redis://localhost:6379/1'
```

### ✅ Setup `celery.py`

Create `myproject/celery.py`:

```python
import os
from celery import Celery

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'myproject.settings')

app = Celery('myproject')
app.config_from_object('django.conf:settings', namespace='CELERY')
app.autodiscover_tasks()
```

In `__init__.py`:

```python
from .celery import app as celery_app
__all__ = ('celery_app',)
```

### ✅ Usage

**tasks.py**

```python
from celery import shared_task

@shared_task(bind=True, max_retries=3)
def send_email(self, name):
    try:
        print(f"Sending email to {name}...")
    except Exception as exc:
        self.retry(exc=exc, countdown=60)  # Retry after 60s
```

**views.py**

```python
from myapp.tasks import send_email
send_email.delay('Bob')
```

### 🔧 Useful Functions

```python
from celery.result import AsyncResult

# Track task by ID
result = AsyncResult(task_id)
status = result.status       # Get status
value = result.result        # Get return value
ready = result.ready()       # Boolean: task finished?
```

**Example: Chain multiple tasks**

```python
from celery import chain
chain(task1.s(), task2.s(), task3.s()).apply_async()
```

### 🧪 Run Workers

```bash
celery -A myproject worker --loglevel=info
```

---

## 3. django-redis

### 🔷 What is it?

`django-redis` allows you to use Redis as a **cache backend** for Django. It supports per-view and low-level caching.

### 🔧 Installation

```bash
pip install django-redis
```

### ⚙️ Configuration in `settings.py`

```python
CACHES = {
    'default': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': 'redis://127.0.0.1:6379/1',
        'OPTIONS': {
            'CLIENT_CLASS': 'django_redis.client.DefaultClient',
        }
    }
}
```

### ✅ Usage

**Basic operations**

```python
from django.core.cache import cache

cache.set("user:1", "kashif", timeout=60)
print(cache.get("user:1"))
cache.delete("user:1")
```

**Advanced django-redis functions**

```python
from django_redis import get_redis_connection

r = get_redis_connection("default")
r.set("mykey", "value")
print(r.get("mykey"))
r.incr("counter")
```

**More examples**

```python
# Store complex data as JSON
import json
r.set("profile:123", json.dumps({"name": "Kashif"}))
profile = json.loads(r.get("profile:123"))

# Use pipelines for efficiency
pipe = r.pipeline()
pipe.set("a", 1)
pipe.incr("a")
pipe.execute()
```

**Per-view caching**

```python
from django.views.decorators.cache import cache_page

@cache_page(60 * 15)
def my_view(request):
    ...
```

**Fragment caching in templates**

```django
{% load cache %}
{% cache 600 sidebar %}
  ... sidebar content ...
{% endcache %}
```

---

## 🧠 Summary Table

| Tool             | Use Case                   | Main Redis Type Used | Best For                     |
| ---------------- | -------------------------- | -------------------- | ---------------------------- |
| `django_rq`      | Background job queue       | List, Hash           | Simple tasks, admin control  |
| `Celery + Redis` | Distributed task queue     | List, Hash, Set      | Complex tasks, chaining      |
| `django-redis`   | Caching (views, data, ORM) | String, Hash         | Fast page/load optimizations |

---

You can start with `django_rq` for quick background tasks, move to `Celery` for production-level async processing, and use `django-redis` to supercharge Django caching.
