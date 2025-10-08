# JavaScript Closures — Complete Reference with Examples

Closures are one of the most powerful and misunderstood features in JavaScript. They allow functions to **remember** the scope in which they were created — even after that scope has finished executing.

---

## 🧠 Table of Contents

1. [What is a Closure?](#what-is-a-closure)
2. [Basic Example](#basic-example)
3. [How Closures Work](#how-closures-work)
4. [Lexical Scope and Closure Relationship](#lexical-scope-and-closure-relationship)
5. [Practical Use Cases](#practical-use-cases)

   * Data Privacy / Encapsulation
   * Function Factories
   * Maintaining State
   * Event Handlers
   * Memoization
6. [Common Pitfalls](#common-pitfalls)
7. [Visualizing Closures](#visualizing-closures)
8. [Closures in Loops](#closures-in-loops)
9. [Memory Implications](#memory-implications)
10. [Summary Table](#summary-table)

---

## 🔹 What is a Closure?

A **closure** is formed when a function is able to **remember and access** its **lexical scope**, even when that function is executed outside its original scope.

```js
function outer() {
  let counter = 0;
  function inner() {
    counter++;
    console.log(counter);
  }
  return inner;
}

const fn = outer();
fn(); // 1
fn(); // 2
fn(); // 3
```

Even though `outer()` has finished execution, `inner()` still remembers and can access `counter`.

---

## 🔹 Basic Example

```js
function greet(name) {
  return function() {
    console.log(`Hello, ${name}`);
  };
}

const sayHi = greet('Alice');
sayHi(); // Hello, Alice
```

Here, `sayHi()` still has access to `name` because of the closure.

---

## 🔹 How Closures Work

1. When a function is defined, it remembers the **environment** (variables, parent scope) where it was created.
2. When returned or passed, it **carries that environment** with it.

```js
function makeCounter() {
  let count = 0;
  return function() {
    count++;
    return count;
  };
}

const counterA = makeCounter();
console.log(counterA()); // 1
console.log(counterA()); // 2
console.log(counterA()); // 3
```

Each instance of `makeCounter()` gets its own copy of `count`.

---

## 🔹 Lexical Scope and Closure Relationship

Closures rely on **lexical scope**, meaning variables are resolved by where functions are **defined**, not **called**.

```js
function outer() {
  let outerVar = 'I am from outer';
  function inner() {
    console.log(outerVar);
  }
  return inner;
}

const fn = outer();
fn(); // I am from outer
```

---

## 🔹 Practical Use Cases

### 1. Data Privacy / Encapsulation

Closures can emulate **private variables**.

```js
function createAccount(initialBalance) {
  let balance = initialBalance;

  return {
    deposit(amount) { balance += amount; },
    withdraw(amount) { balance -= amount; },
    getBalance() { return balance; }
  };
}

const account = createAccount(100);
account.deposit(50);
console.log(account.getBalance()); // 150
```

`balance` is not accessible directly from outside.

---

### 2. Function Factories

Generate specialized functions using closures.

```js
function makeMultiplier(multiplier) {
  return function(num) {
    return num * multiplier;
  };
}

const double = makeMultiplier(2);
const triple = makeMultiplier(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15
```

---

### 3. Maintaining State

Closures allow functions to maintain state between calls.

```js
function createCounter() {
  let count = 0;
  return () => ++count;
}

const counter = createCounter();
console.log(counter()); // 1
console.log(counter()); // 2
console.log(counter()); // 3
```

---

### 4. Event Handlers

Closures can preserve variables inside event listeners.

```js
function setupButton(id) {
  let clicks = 0;
  document.getElementById(id).addEventListener('click', function() {
    clicks++;
    console.log(`Button clicked ${clicks} times`);
  });
}
setupButton('btn1');
```

---

### 5. Memoization

Closures help cache results for performance optimization.

```js
function memoize(fn) {
  const cache = {};
  return function(x) {
    if (cache[x]) return cache[x];
    console.log('Computing for', x);
    cache[x] = fn(x);
    return cache[x];
  };
}

const square = memoize(x => x * x);
console.log(square(4)); // Computing for 4 → 16
console.log(square(4)); // Cached → 16
```

---

## 🔹 Common Pitfalls

### ❌ Shared closure state

All closures share the same reference if created inside the same scope.

```js
const arr = [];
for (var i = 0; i < 3; i++) {
  arr.push(function() { console.log(i); });
}
arr[0](); // 3
arr[1](); // 3
arr[2](); // 3
```

✅ Fix using `let` or IIFE:

```js
for (let i = 0; i < 3; i++) {
  arr.push(() => console.log(i));
}
arr[0](); // 0
arr[1](); // 1
arr[2](); // 2
```

---

## 🔹 Visualizing Closures

```js
function outer() {
  let a = 10;
  return function inner() {
    console.log(a);
  };
}

const closureFn = outer();
closureFn(); // 10
```

Even after `outer()` is finished, `a` remains in memory because `inner()` still references it.

---

## 🔹 Closures in Loops

Each loop iteration can capture the loop variable.

```js
for (let i = 1; i <= 3; i++) {
  setTimeout(() => console.log(i), 1000);
}
// 1, 2, 3
```

If you use `var`, all closures share the same reference:

```js
for (var i = 1; i <= 3; i++) {
  setTimeout(() => console.log(i), 1000);
}
// 4, 4, 4
```

---

## 🔹 Memory Implications

Closures keep variables **alive** as long as the closure exists. This can cause **memory leaks** if not managed properly (e.g., in event listeners or timers).

**Best Practice:**

* Remove listeners when not needed.
* Nullify large references inside closures if no longer required.

---

## 🔹 Summary Table

| Concept          | Description                          | Example                          |
| ---------------- | ------------------------------------ | -------------------------------- |
| Definition       | Function remembering outer variables | `function outer(){return inner}` |
| Lexical Scope    | Variables resolved where defined     | Outer → Inner                    |
| Data Privacy     | Private variable pattern             | Encapsulate `balance`            |
| Function Factory | Returns customized functions         | `makeMultiplier()`               |
| State Retention  | Stores state between calls           | `createCounter()`                |
| Memoization      | Cache results                        | `memoize(fn)`                    |
| Pitfall          | Shared `var` reference               | Fixed by `let` or IIFE           |

---

### ✅ In Short

* **Closure = Function + Lexical Environment**
* Functions retain access to their creation scope.
* Great for encapsulation, state, and optimization.
* Beware of unintentional memory retention.

---

**Next Suggested Topic:** ⚡ *JavaScript Promises & Async/Await — Complete Reference with Examples*
