Perfect 💯 — you want the **entire Redis documentation in Python and Django**, with *every usage type* —
✅ key-value
✅ list
✅ hash
✅ set
✅ sorted set
✅ expiry
✅ pipeline
✅ pub/sub
✅ other operations
— **for all 3 usage types**:
1️⃣ Python `redis`
2️⃣ Django cache (`cache`)
3️⃣ Django + raw Redis (`get_redis_connection`)

Let’s make a **complete master document**, cleanly structured, full code for all.
Ready? Here we go 👇

---

# 🧠 REDIS IN PYTHON AND DJANGO — FULL USAGE GUIDE 🚀

---

## ⚙️ SETUP

### 🔹 Redis server

Run Redis locally or via Docker:

```bash
docker run -d --name redis-server -p 6379:6379 redis
```

---

## 🧩 PART 1 — PURE PYTHON (`import redis`)

### 📦 Install

```bash
pip install redis
```

### 📘 Connect

```python
import redis
r = redis.StrictRedis(host='localhost', port=6379, db=0)
```

---

### 🔑 1. String operations

```python
r.set('name', 'Kashif')
r.get('name')                   # b'Kashif'
r.set('age', 25)
r.incr('age')                   # 26
r.decr('age')                   # 25
r.mset({'a': 1, 'b': 2})
r.mget('a', 'b')                # [b'1', b'2']
r.delete('name')
```

---

### 📜 2. List operations

```python
r.lpush('tasks', 'task1')
r.rpush('tasks', 'task2')
r.lrange('tasks', 0, -1)        # [b'task2', b'task1']
r.lpop('tasks')                 # b'task2'
r.rpop('tasks')                 # b'task1'
r.llen('tasks')
```

---

### 🧩 3. Set operations

```python
r.sadd('users', 'Alice', 'Bob')
r.smembers('users')             # {b'Alice', b'Bob'}
r.sismember('users', 'Alice')
r.srem('users', 'Alice')
r.scard('users')
```

---

### 🏷️ 4. Hash operations

```python
r.hset('user:1', 'name', 'Kashif')
r.hset('user:1', 'email', 'kashif@example.com')
r.hget('user:1', 'name')
r.hgetall('user:1')
r.hkeys('user:1')
r.hvals('user:1')
r.hlen('user:1')
```

---

### 🧮 5. Sorted sets

```python
r.zadd('scores', {'Alice': 100, 'Bob': 80})
r.zrange('scores', 0, -1, withscores=True)
r.zrevrange('scores', 0, -1, withscores=True)
r.zincrby('scores', 10, 'Bob')
r.zscore('scores', 'Bob')
r.zrank('scores', 'Alice')
```

---

### ⏳ 6. Expiry & TTL

```python
r.set('otp', '123456', ex=30)
r.ttl('otp')                    # seconds remaining
r.persist('otp')                # remove expiry
r.expire('otp', 60)             # add expiry
```

---

### ⚙️ 7. Pipeline (batch operations)

```python
pipe = r.pipeline(transaction=True)
pipe.set('a', 1)
pipe.incr('a')
pipe.set('b', 2)
pipe.execute()
```

---

### 📡 8. Pub/Sub

**Publisher:**

```python
publisher = r
publisher.publish('news', 'Breaking: Redis works!')
```

**Subscriber:**

```python
subscriber = r.pubsub()
subscriber.subscribe('news')
for msg in subscriber.listen():
    if msg['type'] == 'message':
        print("Received:", msg['data'])
```

---

### 🧹 9. Utility operations

```python
r.keys('*')
r.exists('name')
r.rename('name', 'username')
r.flushdb()
r.dbsize()
r.info()
```

---

## 🧩 PART 2 — DJANGO CACHE (High-Level, via `cache`)

### 📦 Install

```bash
pip install django-redis
```

### ⚙️ settings.py

```python
CACHES = {
    "default": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://127.0.0.1:6379/1",
        "OPTIONS": {"CLIENT_CLASS": "django_redis.client.DefaultClient"},
    }
}
```

---

### 🧠 Basic operations

```python
from django.core.cache import cache

cache.set('name', 'Kashif', timeout=60)
cache.get('name')           # 'Kashif'
cache.delete('name')
```

---

### 🔁 Multiple keys

```python
cache.set_many({'a': 1, 'b': 2})
cache.get_many(['a', 'b'])  # {'a': 1, 'b': 2}
cache.delete_many(['a', 'b'])
```

---

### ⏱ Timeout / Expiry

```python
cache.set('otp', '123456', timeout=30)
cache.ttl('otp')             # seconds remaining (django-redis specific)
```

---

### 📈 Increment / Decrement

```python
cache.set('counter', 5)
cache.incr('counter')        # 6
cache.decr('counter')        # 5
```

---

### 📜 Clear Cache

```python
cache.clear()
```

---

### 💡 Common use cases

```python
# Cache API response
cache.set('users', users_data, timeout=300)

# Cache a view
from django.views.decorators.cache import cache_page

@cache_page(60 * 5)
def my_view(request):
    ...
```

---

## 🧩 PART 3 — DJANGO + RAW REDIS (`get_redis_connection`)

> When you need **real Redis commands** inside Django.

---

### 📘 Connect

```python
from django_redis import get_redis_connection
r = get_redis_connection("default")
```

---

### 🔑 1. Strings

```python
r.set('name', 'Kashif')
r.get('name')
r.mset({'a': 1, 'b': 2})
r.mget('a', 'b')
r.delete('a')
```

---

### 📜 2. Lists

```python
r.lpush('queue', 'job1')
r.rpush('queue', 'job2')
r.lpop('queue')
r.rpop('queue')
r.lrange('queue', 0, -1)
r.llen('queue')
```

---

### 🧩 3. Sets

```python
r.sadd('online_users', 'Alice', 'Bob')
r.smembers('online_users')
r.srem('online_users', 'Bob')
r.scard('online_users')
```

---

### 🏷️ 4. Hashes

```python
r.hset('user:1', 'email', 'kashif@example.com')
r.hset('user:1', 'name', 'Kashif')
r.hget('user:1', 'name')
r.hgetall('user:1')
r.hdel('user:1', 'email')
```

---

### 🧮 5. Sorted sets

```python
r.zadd('leaderboard', {'Alice': 90, 'Bob': 100})
r.zrange('leaderboard', 0, -1, withscores=True)
r.zrevrange('leaderboard', 0, -1, withscores=True)
r.zincrby('leaderboard', 10, 'Alice')
```

---

### ⏳ 6. Expiry

```python
r.set('otp', '9876', ex=60)
r.ttl('otp')
r.expire('otp', 120)
```

---

### ⚙️ 7. Pipelines

```python
pipe = r.pipeline(transaction=True)
pipe.set('a', 1)
pipe.incr('a')
pipe.set('b', 2)
pipe.execute()
```

---

### 📡 8. Pub/Sub

**Publisher**

```python
publisher = get_redis_connection("default")
publisher.publish('news', 'Redis + Django rocks!')
```

**Subscriber**

```python
subscriber = get_redis_connection("default").pubsub()
subscriber.subscribe('news')

for msg in subscriber.listen():
    if msg['type'] == 'message':
        print("Received:", msg['data'])
```

---

### 🧹 9. Utility operations

```python
r.keys('*')
r.exists('user:1')
r.flushdb()
r.dbsize()
r.info()
```

---

# 🧾 FINAL SUMMARY TABLE

| Feature                   | Pure Python (`redis`) | Django Cache (`cache`)    | Django Raw Redis (`get_redis_connection`) |
| ------------------------- | --------------------- | ------------------------- | ----------------------------------------- |
| Setup                     | `import redis`        | `django-redis` + CACHES   | same CACHES                               |
| Level                     | Low-level             | High-level (abstracted)   | Low-level                                 |
| Supports Django cache API | ❌                     | ✅                         | ❌                                         |
| Full Redis features       | ✅                     | ❌                         | ✅                                         |
| Example                   | `r.set('key', 'val')` | `cache.set('key', 'val')` | `conn.set('key', 'val')`                  |
| Pub/Sub                   | ✅                     | ❌                         | ✅                                         |
| Pipelines                 | ✅                     | ❌                         | ✅                                         |
| TTL / Expiry              | ✅                     | ✅                         | ✅                                         |
| Session backend           | ❌                     | ✅                         | ✅ (manually)                              |

---
