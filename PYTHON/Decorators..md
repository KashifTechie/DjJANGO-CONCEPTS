# **Python Decorators: Complete Guide**

A **decorator** in Python is a function that **modifies the behavior of another function**. They are commonly used for logging, access control, timing, or repeating tasks.

---

## **1️⃣ Normal Decorators**

### **Definition**

A normal decorator takes a function as input, wraps it in another function, and returns the wrapper.

```python
def my_decorator(func):
    def wrapper():
        print("Before calling the function")
        func()
        print("After calling the function")
    return wrapper
```

* `func` → the original function.
* `wrapper` → the new function that adds extra behavior.
* `my_decorator` → returns the `wrapper`.

---

### **Usage**

You can apply the decorator using `@`:

```python
@my_decorator
def say_hello():
    print("Hello!")

say_hello()
```

**Output:**

```
Before calling the function
Hello!
After calling the function
```

**What happens internally?**

```python
say_hello = my_decorator(say_hello)
say_hello()
```

---

### **Example with arguments**

Normal decorators can also handle functions with arguments using `*args` and `**kwargs`:

```python
def my_decorator(func):
    def wrapper(*args, **kwargs):
        print("Before function call")
        func(*args, **kwargs)
        print("After function call")
    return wrapper

@my_decorator
def greet(name):
    print(f"Hello {name}!")

greet("Alice")
```

**Output:**

```
Before function call
Hello Alice!
After function call
```

---

## **2️⃣ Parameterized Decorators (Decorator with Arguments)**

Sometimes, you want the decorator itself to take **parameters**.
For example, repeat a function **n times**.

---

### **Definition**

```python
def repeat(times):           # 1️⃣ Outer function takes arguments
    def decorator(func):     # 2️⃣ Actual decorator
        def wrapper(*args, **kwargs):  # 3️⃣ Wrapper function
            for _ in range(times):
                func(*args, **kwargs)
        return wrapper
    return decorator
```

* `repeat(times)` → receives parameter `times` and returns a decorator.
* `decorator(func)` → standard decorator, receives the function to wrap.
* `wrapper(*args, **kwargs)` → does the repeated calling.

---

### **Usage**

```python
@repeat(3)
def greet(name):
    print(f"Hello {name}!")

greet("Alice")
```

**Output:**

```
Hello Alice!
Hello Alice!
Hello Alice!
```

**What happens internally?**

```python
# Step 1: repeat(3) returns decorator
decorator_func = repeat(3)

# Step 2: decorator_func wraps greet
greet = decorator_func(greet)

# Step 3: greet() is actually wrapper()
greet("Alice")
```

---

### **Example with multiple decorator arguments**

```python
def repeat_with_prefix(times, prefix):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(times):
                print(prefix, end=" ")
                func(*args, **kwargs)
        return wrapper
    return decorator

@repeat_with_prefix(2, ">>>")
def greet(name):
    print(f"Hello {name}!")

greet("Bob")
```

**Output:**

```
>>> Hello Bob!
>>> Hello Bob!
```

---

## **3️⃣ Key Points**

| Feature                     | Normal Decorator                | Parameterized Decorator                     |
| --------------------------- | ------------------------------- | ------------------------------------------- |
| Accepts function arguments  | Yes, via `*args` and `**kwargs` | Yes, via `*args` and `**kwargs`             |
| Accepts decorator arguments | No                              | Yes, outer function handles it              |
| Structure                   | `decorator(func) -> wrapper`    | `outer(args) -> decorator(func) -> wrapper` |
| Syntax sugar                | `@decorator`                    | `@decorator(arg1, arg2)`                    |

---

## **4️⃣ Summary Diagram**

**Normal decorator:**

```
@decorator
def func():
    pass

# Internally:
func = decorator(func)
```

**Parameterized decorator:**

```
@decorator(arg)
def func():
    pass

# Internally:
decorator_func = decorator(arg)
func = decorator_func(func)
```

---

✅ **Takeaway:**

1. A **normal decorator** wraps a function to modify behavior.
2. A **parameterized decorator** wraps a function but also **takes arguments** to control the behavior dynamically.
3. Use `*args` and `**kwargs` in the wrapper to allow passing any number of arguments.
4. The `@` syntax is just **syntactic sugar** for function reassignment.

---

