# Redis with Django RQ: Full Guide

This guide explains how to use Redis with Django for background jobs and caching, including all major `redis.Redis` methods available through `django_rq.get_connection()`.

---

## ✨ Overview

* Redis: In-memory data store used for caching, queues, and more.
* django-rq: A Django integration for Redis Queue (RQ) to handle background tasks.
* get\_connection(): Returns a raw Redis client to use all Redis commands.

---

## 🔧 Step 1: Install Redis

### Ubuntu/Debian

```bash
sudo apt update
sudo apt install redis-server
sudo systemctl start redis-server
```

### macOS

```bash
brew install redis
brew services start redis
```

### Verify Redis

```bash
redis-cli ping
# Output: PONG
```

---

## 📁 Step 2: Install Required Python Packages

```bash
pip install django-rq
```

---

## 📄 Step 3: Configure Django

### In `settings.py`

```python
INSTALLED_APPS = [
    ...
    'django_rq',
]

RQ_QUEUES = {
    'default': {
        'HOST': '127.0.0.1',
        'PORT': 6379,
        'DB': 0,
        'DEFAULT_TIMEOUT': 360,
    }
}
```

### Optional: Admin Panel for Jobs

```python
# urls.py
from django.urls import path, include

urlpatterns = [
    ...
    path('django-rq/', include('django_rq.urls')),
]
```

---

## 🧠 Step 4: Use `get_connection()`

### Import and Connect

```python
from django_rq import get_connection

redis_conn = get_connection("default")
```

### Example Usage

```python
redis_conn.set("mykey", "value")
print(redis_conn.get("mykey"))
```

---

## 📝 Available Redis Methods via `redis_conn`

### 🔑 Basic Key Operations

| Method                  | Description         |
| ----------------------- | ------------------- |
| `get(name)`             | Get value of a key  |
| `set(name, value)`      | Set a key           |
| `delete(name)`          | Delete a key        |
| `exists(name)`          | Check if key exists |
| `expire(name, seconds)` | Set TTL             |
| `ttl(name)`             | Get TTL             |
| `rename(old, new)`      | Rename key          |
| `keys(pattern)`         | Pattern matching    |

### 👌 List Operations

| Method                     | Description           |
| -------------------------- | --------------------- |
| `lpush(name, value)`       | Push to start of list |
| `rpush(name, value)`       | Push to end of list   |
| `lpop(name)`               | Pop from start        |
| `rpop(name)`               | Pop from end          |
| `lrange(name, start, end)` | Get list slice        |
| `llen(name)`               | Length of list        |

### 📚 Hashes

| Method                      | Description         |
| --------------------------- | ------------------- |
| `hset(name, key, value)`    | Set hash field      |
| `hget(name, key)`           | Get field           |
| `hmget(name, [key1, key2])` | Get multiple fields |
| `hgetall(name)`             | Get entire hash     |
| `hdel(name, key)`           | Delete field        |

### 📈 Counters

| Method                     | Description     |
| -------------------------- | --------------- |
| `incr(name)`               | Increment by 1  |
| `decr(name)`               | Decrement by 1  |
| `incrbyfloat(name, value)` | Increment float |

### 🧠 Sets

| Method                   | Description      |
| ------------------------ | ---------------- |
| `sadd(name, value)`      | Add to set       |
| `srem(name, value)`      | Remove from set  |
| `smembers(name)`         | All set members  |
| `sismember(name, value)` | Check membership |

### 💬 Pub/Sub

| Method                      | Description          |
| --------------------------- | -------------------- |
| `publish(channel, message)` | Publish message      |
| `subscribe(channel)`        | Subscribe to channel |
| `listen()`                  | Listen to channel    |

### ⚖️ Other Useful Methods

| Method       | Description      |
| ------------ | ---------------- |
| `flushdb()`  | Clear current DB |
| `flushall()` | Clear all DBs    |
| `dbsize()`   | Number of keys   |
| `info()`     | Server info      |
| `pipeline()` | Batch operations |

---

## 💼 Summary

* `get_connection("default")` gives full access to Redis commands.
* Use it when low-level Redis access is needed (debugging, manual queue management, counters, etc).
* You can combine this with high-level RQ queue operations for background job management.

---

## 🧪 Example: Using Patterned Keys with `set`, `incr`, `decr`, and `get`

Here's a practical example where you use a key pattern like `user:123:views` to track and manipulate counters for users.

```python
from django_rq import get_connection

redis_conn = get_connection("default")

# Set an initial value
user_id = 123
key = f"user:{user_id}:views"
redis_conn.set(key, 10)

# Increment a user's view count
redis_conn.incr(key)

# Decrement the view count (e.g., on undo)
redis_conn.decr(key)

# Fetch the updated value
current_views = redis_conn.get(key)
print(f"User {user_id} has {int(current_views)} views")
```

You can use key patterns (`user:*:views`) with `keys()` to loop over multiple users.

```python
user_keys = redis_conn.keys("user:*:views")
for key in user_keys:
    print(f"{key.decode()}: {redis_conn.get(key).decode()}")
```

This is a common pattern for counters, metrics, or tracking per-object data in Redis.
