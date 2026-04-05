
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

* Use **`let`** when the value needs to be updated (re-assigned)
* Use **`const`** when the variable should NOT be re-assigned (must be initialized at declaration), the reference cannot change.
* Avoid **`var`** in modern JavaScript due to function scope and hoisting issues


Got it — I cleaned it up so it **matches your existing style perfectly** (same heading levels, spacing, tone, and structure).

You can paste this directly under your notes 👇

---


---

# 🔹 Data Types (Primitive vs Reference)

---

## 🔹 1. Categories

JavaScript data types are divided into:

- Primitive Types
- Reference Types

---

## 🔹 2. Primitive Data Types

👉 Stored by value (independent copies)

### ➤ Types:

- Number
- String
- Boolean
- Undefined
- Null
- BigInt
- Symbol

### ➤ Example:

```javascript
let a = 10;
let b = a;

b = 20;

console.log(a); // 10
console.log(b); // 20


### ➤ Key Points:

* Stored in stack
* Immutable
* Compared by value

---

## 🔹 3. Reference Data Types

👉 Stored by reference (memory address)

### ➤ Types:

* Object
* Array
* Function
* Date
* Map / Set

### ➤ Example:

```javascript
let obj1 = { name: "John" };
let obj2 = obj1;

obj2.name = "Kashif";

console.log(obj1.name); // Kashif
```

### ➤ Key Points:

* Stored in heap
* Mutable
* Compared by reference

---

## 🔹 4. Comparison

### ➤ Primitive:

```javascript
let a = 10;
let b = 10;

console.log(a === b); // true
```

### ➤ Reference:

```javascript
let obj1 = { name: "A" };
let obj2 = { name: "A" };

console.log(obj1 === obj2); // false
```

---

## 🔹 5. Copy Behavior

### ➤ Primitive Copy:

```javascript
let x = 5;
let y = x;

y = 10;

console.log(x); // 5
```

### ➤ Reference Copy:

```javascript
let arr1 = [1, 2];
let arr2 = arr1;

arr2.push(3);

console.log(arr1); // [1, 2, 3]
```

---

## 🔹 6. Copying Reference Safely

### ➤ Shallow Copy:

```javascript
let obj1 = { name: "A" };
let obj2 = { ...obj1 };
```

### ➤ Deep Copy:

```javascript
let obj2 = JSON.parse(JSON.stringify(obj1));
```

---

# 🔥 Final Summary Table

| Feature    | Primitive | Reference    |
| ---------- | --------- | ------------ |
| Storage    | Stack     | Heap         |
| Copy       | Value     | Reference    |
| Mutable    | ❌ No      | ✅ Yes        |
| Comparison | By value  | By reference |

---

# 💡 Key Takeaway

* Primitive → independent copies (safe)
* Reference → shared memory (be careful)
* Use shallow/deep copy when needed

```

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

````md
---

# 🔹 Data Types (Primitive vs Reference)

---

## 🔹 1. Categories

JavaScript data types are divided into:

- Primitive Types
- Reference Types

---

## 🔹 2. Primitive Data Types

👉 Stored by value (independent copies)

### ➤ Types:

- Number
- String
- Boolean
- Undefined
- Null
- BigInt
- Symbol

### ➤ Example:

```javascript
let a = 10;
let b = a;

b = 20;

console.log(a); // 10
console.log(b); // 20
````

### ➤ Key Points:

* Stored in stack
* Immutable
* Compared by value

---

## 🔹 3. Reference Data Types

👉 Stored by reference (memory address)

### ➤ Types:

* Object
* Array
* Function
* Date
* Map / Set

### ➤ Example:

```javascript
let obj1 = { name: "John" };
let obj2 = obj1;

obj2.name = "Kashif";

console.log(obj1.name); // Kashif
```

### ➤ Key Points:

* Stored in heap
* Mutable
* Compared by reference

---

## 🔹 4. Comparison

### ➤ Primitive:

```javascript
let a = 10;
let b = 10;

console.log(a === b); // true
```

### ➤ Reference:

```javascript
let obj1 = { name: "A" };
let obj2 = { name: "A" };

console.log(obj1 === obj2); // false
```

---

## 🔹 5. Copy Behavior

### ➤ Primitive Copy:

```javascript
let x = 5;
let y = x;

y = 10;

console.log(x); // 5
```

### ➤ Reference Copy:

```javascript
let arr1 = [1, 2];
let arr2 = arr1;

arr2.push(3);

console.log(arr1); // [1, 2, 3]
```

---

## 🔹 6. Copying Reference Safely

### ➤ Shallow Copy:

```javascript
let obj1 = { name: "A" };
let obj2 = { ...obj1 };
```

### ➤ Deep Copy:

```javascript
let obj2 = JSON.parse(JSON.stringify(obj1));
```

---

# 🔥 Final Summary Table

| Feature    | Primitive | Reference    |
| ---------- | --------- | ------------ |
| Storage    | Stack     | Heap         |
| Copy       | Value     | Reference    |
| Mutable    | ❌ No      | ✅ Yes        |
| Comparison | By value  | By reference |

---

# 💡 Key Takeaway

* Primitive → independent copies (safe)
* Reference → shared memory (be careful)
* Use shallow/deep copy when needed

```

---

If you want, next I can align this even deeper with your notes by adding:

- 🔥 **Interview traps (VERY important)**
- 🧠 **Stack vs Heap visual explanation**
- ⚠️ **Common bugs developers make with references**

Just tell me 👍
```





