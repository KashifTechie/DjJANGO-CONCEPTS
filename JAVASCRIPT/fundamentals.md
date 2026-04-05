# 📘 JavaScript Fundamentals

## Variables: `var`, `let`, `const`

---

# 🔹 1. Scope (Accessibility)

### ➤ `var` → Function Scoped (NOT block scoped)

```javascript
var x = 10;

if (true) {
    console.log(x); // ✅ 10 (outer → inner)
}

if (true) {
    var y = 20;
}

console.log(y); // ✅ 20 (inner → outer ❗)
```

👉 `var` ignores `{}` blocks and only respects function scope.

---

### ➤ `let` → Block Scoped

```javascript
let x = 10;

if (true) {
    console.log(x); // ✅ 10 (outer → inner)
}

if (true) {
    let y = 20;
}

console.log(y); // ❌ ReferenceError
```

👉 `let` is restricted to the block `{}` where it is declared.

---

### ➤ `const` → Block Scoped (same as `let`)

```javascript
const x = 10;

if (true) {
    console.log(x); // ✅ 10
}

if (true) {
    const y = 20;
}

console.log(y); // ❌ ReferenceError
```

---

# 🔹 2. Re-assignment (Changing Value)

| Keyword | Can re-assign? |
| ------- | -------------- |
| var     | ✅ Yes          |
| let     | ✅ Yes          |
| const   | ❌ No           |

```javascript
var a = 10;
a = 20; // ✅

let b = 10;
b = 20; // ✅

const c = 10;
c = 20; // ❌ TypeError
```

---

# 🔹 3. Re-declaration (Same Variable Name)

| Keyword | Can re-declare in same scope? |
| ------- | ----------------------------- |
| var     | ✅ Yes                         |
| let     | ❌ No                          |
| const   | ❌ No                          |

```javascript
var x = 10;
var x = 20; // ✅

let y = 10;
let y = 20; // ❌ SyntaxError

const z = 10;
const z = 20; // ❌ SyntaxError
```

---

# 🔹 4. Declaration vs Initialization

### ➤ `var`

```javascript
var x;
console.log(x); // ✅ undefined
```

👉 Automatically initialized with `undefined`

---

### ➤ `let`

```javascript
let x;
console.log(x); // ✅ undefined
```

👉 Declared but not initialized → still `undefined`

---

### ➤ `const`

```javascript
const x; // ❌ SyntaxError
```

👉 Must be initialized at the time of declaration

---

# 🔹 5. Access Before Initialization (Hoisting)

### ➤ `var`

```javascript
console.log(x); // ✅ undefined
var x = 10;
```

👉 Hoisted and initialized with `undefined`

---

### ➤ `let`

```javascript
console.log(x); // ❌ ReferenceError
let x = 10;
```

👉 Temporal Dead Zone (TDZ)

---

### ➤ `const`

```javascript
console.log(x); // ❌ ReferenceError
const x = 10;
```

👉 Same TDZ behavior as `let`

---

# 🔥 Final Summary Table

| Feature              | var           | let     | const   |
| -------------------- | ------------- | ------- | ------- |
| Scope                | Function      | Block   | Block   |
| Outer → Inner Access | ✅             | ✅       | ✅       |
| Inner → Outer Access | ✅             | ❌       | ❌       |
| Re-assign            | ✅             | ✅       | ❌       |
| Re-declare           | ✅             | ❌       | ❌       |
| Declare without init | ✅             | ✅       | ❌       |
| Access before init   | ✅ (undefined) | ❌ (TDZ) | ❌ (TDZ) |

---

# 💡 Key Takeaway

* Use **`let`** for variables that change
* Use **`const`** by default
* Avoid **`var`** in modern JavaScript


Got it — I cleaned it up so it **matches your existing style perfectly** (same heading levels, spacing, tone, and structure).

You can paste this directly under your notes 👇

---

