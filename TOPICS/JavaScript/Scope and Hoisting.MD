# JavaScript Scope and Hoisting — Complete Reference with Examples

Understanding **scope** and **hoisting** is crucial to mastering JavaScript. They determine how variables and functions are accessed and executed.

---

## 🧠 Table of Contents

1. [What is Scope?](#what-is-scope)
2. [Types of Scope](#types-of-scope)

   * Global Scope
   * Function Scope
   * Block Scope
   * Lexical Scope
3. [Scope Chain](#scope-chain)
4. [Variable Declarations (`var`, `let`, `const`)](#variable-declarations-var-let-const)
5. [What is Hoisting?](#what-is-hoisting)
6. [Hoisting with `var`](#hoisting-with-var)
7. [Hoisting with `let` and `const`](#hoisting-with-let-and-const)
8. [Function Hoisting](#function-hoisting)
9. [Temporal Dead Zone (TDZ)](#temporal-dead-zone-tdz)
10. [Practical Examples](#practical-examples)
11. [Common Pitfalls](#common-pitfalls)
12. [Summary Table](#summary-table)

---

## 🔹 What is Scope?

Scope defines the **visibility** and **lifetime** of variables and functions — where you can access them.

```js
let name = 'John'; // Global Scope

function greet() {
  let message = 'Hello'; // Local (Function) Scope
  console.log(message + ' ' + name);
}

greet(); // Hello John
console.log(message); // ❌ ReferenceError
```

---

## 🔹 Types of Scope

### 1. Global Scope

Variables declared **outside** of any function or block are globally accessible.

```js
var globalVar = 'I am global';

function test() {
  console.log(globalVar); // Accessible
}
test();
```

### 2. Function Scope

`var` creates variables scoped to the **function**.

```js
function example() {
  var funcScoped = 'Visible only inside this function';
  console.log(funcScoped);
}
example();
console.log(funcScoped); // ❌ ReferenceError
```

### 3. Block Scope

`let` and `const` create variables limited to the **block `{}`** they’re defined in.

```js
if (true) {
  let blockScoped = 'Inside block';
  const alsoBlock = 'Still inside block';
}
console.log(blockScoped); // ❌ ReferenceError
```

### 4. Lexical Scope

Nested functions have access to variables in their **outer scope**.

```js
function outer() {
  let outerVar = 'outer';
  function inner() {
    console.log(outerVar); // Access outer variable
  }
  inner();
}
outer();
```

---

## 🔹 Scope Chain

When a variable is used, JS searches:

1. **Local scope** → 2. **Outer function scope** → 3. **Global scope**

```js
let a = 10;
function first() {
  let b = 20;
  function second() {
    let c = 30;
    console.log(a + b + c); // 60
  }
  second();
}
first();
```

If a variable isn’t found in the local scope, it moves outward until it reaches global scope.

---

## 🔹 Variable Declarations (`var`, `let`, `const`)

| Keyword | Scope Type | Re-declarable | Re-assignable | Hoisted                          |
| ------- | ---------- | ------------- | ------------- | -------------------------------- |
| `var`   | Function   | ✅ Yes         | ✅ Yes         | ✅ Yes (initialized as undefined) |
| `let`   | Block      | ❌ No          | ✅ Yes         | ✅ Yes (TDZ applies)              |
| `const` | Block      | ❌ No          | ❌ No          | ✅ Yes (TDZ applies)              |

```js
var x = 1;
let y = 2;
const z = 3;
```

---

## 🔹 What is Hoisting?

**Hoisting** is JavaScript’s behavior of moving **declarations** (not initializations) to the top of their scope before code execution.

Think of it as JS doing this behind the scenes:

```js
console.log(a); // undefined (due to hoisting)
var a = 5;
```

Internally behaves like:

```js
var a;
console.log(a); // undefined
a = 5;
```

---

## 🔹 Hoisting with `var`

`var` declarations are hoisted and initialized as `undefined`.

```js
console.log(x); // undefined
var x = 10;
```

Equivalent to:

```js
var x;
console.log(x);
x = 10;
```

---

## 🔹 Hoisting with `let` and `const`

`let` and `const` are hoisted **but not initialized**. Accessing them before declaration causes a **ReferenceError**.

```js
console.log(y); // ❌ ReferenceError
let y = 10;
```

This is known as the **Temporal Dead Zone (TDZ)** — the time between entering scope and actual declaration.

---

## 🔹 Function Hoisting

### Function Declaration — ✅ Hoisted

```js
sayHello(); // Works
function sayHello() {
  console.log('Hello World');
}
```

### Function Expression — ❌ Not Hoisted

```js
sayHi(); // ❌ TypeError
var sayHi = function() {
  console.log('Hi!');
};
```

---

## 🔹 Temporal Dead Zone (TDZ)

The TDZ exists from the start of a block until the variable is declared.

```js
{
  // TDZ starts
  console.log(a); // ❌ ReferenceError
  let a = 5; // TDZ ends
  console.log(a); // 5
}
```

---

## 🔹 Practical Examples

### Example 1: `var` vs `let`

```js
if (true) {
  var a = 1;
  let b = 2;
}
console.log(a); // 1
console.log(b); // ❌ ReferenceError
```

### Example 2: Function Scope + Hoisting

```js
function demo() {
  console.log(x); // undefined
  var x = 5;
  console.log(x); // 5
}
demo();
```

### Example 3: Lexical Scope Chain

```js
let x = 1;
function outer() {
  let y = 2;
  function inner() {
    let z = 3;
    console.log(x + y + z);
  }
  inner();
}
outer(); // 6
```

---

## 🔹 Common Pitfalls

### ❌ Misunderstanding `var` hoisting

```js
console.log(a); // undefined
var a = 10;
```

**Fix:** Always declare variables before using them.

### ❌ Using variables before declaration (TDZ)

```js
console.log(b); // ReferenceError
let b = 5;
```

### ❌ Shadowing Variables

```js
let count = 1;
function counter() {
  let count = 2;
  console.log(count);
}
counter(); // 2
console.log(count); // 1
```

---

## 🔹 Summary Table

| Concept           | Behavior                                     | Example                                    |
| ----------------- | -------------------------------------------- | ------------------------------------------ |
| Global Scope      | Accessible everywhere                        | `let a = 1;`                               |
| Function Scope    | Accessible inside function only              | `var x = 5;` inside function               |
| Block Scope       | Exists only inside `{}`                      | `let`, `const`                             |
| Lexical Scope     | Inner functions access outer variables       | nested functions                           |
| Hoisting          | Declarations moved to top                    | `var` and functions hoisted                |
| TDZ               | Area where variable exists but uninitialized | affects `let` and `const`                  |
| Function Hoisting | Declarations hoisted                         | `function fn(){}` works before declaration |

---

### ✅ In Short

* `var` = function-scoped + hoisted (initialized as `undefined`)
* `let` & `const` = block-scoped + hoisted (TDZ applies)
* Functions declared with `function` are hoisted fully.
* Always **declare before use** to avoid hoisting confusion.

---

**Next Suggested Topic:** ⚙️ *JavaScript Closures — Complete Reference with Examples*
