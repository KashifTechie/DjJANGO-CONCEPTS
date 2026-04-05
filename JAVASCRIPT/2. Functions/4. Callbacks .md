# Callbacks

---

# 🔹 1. What is a Callback?

A **function passed as an argument** to another function, to be **called later** inside that function.

```
Normal function   → you call it directly
Callback function → you pass it, someone else calls it
```

```javascript
function greet(name, callback) {
    console.log("Hello, " + name);
    callback();              // calling the passed function
}

function sayBye() {
    console.log("Goodbye!");
}

greet("Kashif", sayBye);
// Hello, Kashif
// Goodbye!
```

---

# 🔹 2. How it Works Step by Step

```
1. You define a function
2. You pass it as an argument to another function
3. That function calls it at the right time
4. Your function runs
```

```javascript
function doTask(callback) {
    console.log("Task started");   // step 1
    console.log("Task finished");  // step 2
    callback();                    // step 3 → now run your function
}

function onDone() {
    console.log("Callback ran!");  // step 4
}

doTask(onDone);
// Task started
// Task finished
// Callback ran!
```

---

# 🔹 3. Types of Callbacks

```
1. Named Callback      → passing a named function
2. Anonymous Callback  → passing a function directly
3. Arrow Callback      → passing an arrow function
4. Synchronous         → runs immediately, in order
5. Asynchronous        → runs later, after something finishes
```

---

# 🔹 4. Named Callback

A separately defined function passed by name.

```javascript
function printMessage() {
    console.log("Task is done!");
}

function runTask(callback) {
    console.log("Running task...");
    callback();
}

runTask(printMessage);
// Running task...
// Task is done!
```

---

### ➤ With Arguments

```javascript
function showResult(result) {
    console.log("Result is: " + result);
}

function calculate(a, b, callback) {
    let sum = a + b;
    callback(sum);
}

calculate(5, 10, showResult);
// Result is: 15
```

---

# 🔹 5. Anonymous Callback

A function passed **directly** without a name.

```javascript
function runTask(callback) {
    console.log("Running task...");
    callback();
}

runTask(function() {
    console.log("Task is done!");
});
// Running task...
// Task is done!
```

---

### ➤ With Arguments

```javascript
function calculate(a, b, callback) {
    let sum = a + b;
    callback(sum);
}

calculate(5, 10, function(result) {
    console.log("Result is: " + result);
});
// Result is: 15
```

---

# 🔹 6. Arrow Callback

Same as anonymous but using arrow function syntax.

```javascript
function runTask(callback) {
    console.log("Running task...");
    callback();
}

runTask(() => {
    console.log("Task is done!");
});
// Running task...
// Task is done!
```

---

### ➤ With Arguments

```javascript
function calculate(a, b, callback) {
    let sum = a + b;
    callback(sum);
}

calculate(5, 10, result => {
    console.log("Result is: " + result);
});
// Result is: 15
```

---

# 🔹 7. Synchronous Callback

Runs **immediately** and **in order** — no waiting.

---

### ➤ Basic Example

```javascript
function greet(name, callback) {
    console.log("Hello, " + name);
    callback();
}

greet("Kashif", () => {
    console.log("Nice to meet you!");
});
// Hello, Kashif
// Nice to meet you!
```

---

### ➤ Real Example — Custom Filter

```javascript
function filterNumbers(numbers, condition) {
    let result = [];
    for (let num of numbers) {
        if (condition(num)) {
            result.push(num);
        }
    }
    return result;
}

let numbers = [1, 2, 3, 4, 5, 6];

let evens = filterNumbers(numbers, n => n % 2 === 0);
let odds  = filterNumbers(numbers, n => n % 2 !== 0);

console.log(evens); // [2, 4, 6]
console.log(odds);  // [1, 3, 5]
```

---

### ➤ Real Example — Custom Transform

```javascript
function transformList(items, transform) {
    let result = [];
    for (let item of items) {
        result.push(transform(item));
    }
    return result;
}

let prices = [100, 200, 300];

let discounted = transformList(prices, price => price * 0.9);

console.log(discounted); // [90, 180, 270]
```

---

# 🔹 8. Asynchronous Callback

Runs **later** — after a delay or after something finishes (file read, API call, timer).

---

### ➤ Basic Example — `setTimeout`

```javascript
console.log("Start");

setTimeout(() => {
    console.log("I ran after 2 seconds");
}, 2000);

console.log("End");

// Start
// End
// I ran after 2 seconds  ← runs after 2s, not in order
```

---

### ➤ Real Example — Simulate API Call

```javascript
function fetchUser(id, callback) {
    console.log("Fetching user...");

    setTimeout(() => {
        let user = { id: id, name: "Kashif", age: 22 };
        callback(user);
    }, 2000);
}

fetchUser(1, user => {
    console.log("User received:", user);
});

// Fetching user...
// (2 seconds later...)
// User received: { id: 1, name: "Kashif", age: 22 }
```

---

### ➤ Real Example — Success and Error Callbacks

```javascript
function fetchData(id, onSuccess, onError) {
    setTimeout(() => {
        if (id > 0) {
            let data = { id: id, name: "Kashif" };
            onSuccess(data);
        } else {
            onError("Invalid ID");
        }
    }, 1000);
}

fetchData(
    1,
    data  => console.log("Success:", data),
    error => console.log("Error:", error)
);

// Success: { id: 1, name: "Kashif" }

fetchData(
    -1,
    data  => console.log("Success:", data),
    error => console.log("Error:", error)
);

// Error: Invalid ID
```

---

# 🔹 9. Callback Hell

When callbacks are **nested inside callbacks** — becomes hard to read and maintain.

```javascript
// ❌ BAD — Callback Hell
fetchUser(1, user => {
    fetchOrders(user.id, orders => {
        fetchOrderDetails(orders[0].id, details => {
            fetchPayment(details.paymentId, payment => {
                console.log("Payment:", payment);
            });
        });
    });
});
```

```
Problem:
→ Hard to read
→ Hard to debug
→ Hard to maintain
→ Called "Pyramid of Doom"
```

---

### ➤ How to Avoid — Use Named Functions

```javascript
// ✅ BETTER — break into named functions

function handlePayment(payment) {
    console.log("Payment:", payment);
}

function handleDetails(details) {
    fetchPayment(details.paymentId, handlePayment);
}

function handleOrders(orders) {
    fetchOrderDetails(orders[0].id, handleDetails);
}

function handleUser(user) {
    fetchOrders(user.id, handleOrders);
}

fetchUser(1, handleUser); // clean, readable
```

> ✅ The real solution to Callback Hell is **Promises** and **async/await** — covered in the next topic.

---

# 🔹 10. Built-in Callbacks You Already Use

```javascript
// map
[1, 2, 3].map(n => n * 2);

// filter
[1, 2, 3].filter(n => n > 1);

// forEach
[1, 2, 3].forEach(n => console.log(n));

// setTimeout
setTimeout(() => console.log("done"), 1000);

// addEventListener
button.addEventListener("click", () => {
    console.log("clicked");
});
```

> All of these are passing a **callback function** to be called later.

---

# 🔥 Final Summary Table

| Type | Runs When | Example |
|------|-----------|---------|
| Named Callback | Immediately or later | `run(myFn)` |
| Anonymous Callback | Immediately or later | `run(function() {})` |
| Arrow Callback | Immediately or later | `run(() => {})` |
| Synchronous Callback | Immediately, in order | `filter()`, `map()` |
| Asynchronous Callback | Later, after delay/event | `setTimeout`, API calls |

---

# 💡 Key Takeaway

- A **callback** is just a function passed to another function to call later
- **Synchronous callbacks** run immediately in order
- **Asynchronous callbacks** run later after delay or event
- Always handle **success and error** in async callbacks
- **Callback hell** happens with deep nesting — break into named functions or use Promises
