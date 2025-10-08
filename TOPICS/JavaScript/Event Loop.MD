# JavaScript Event Loop — Complete Reference with Examples

The **JavaScript Event Loop** is the engine behind how JavaScript handles **asynchronous operations** like callbacks, promises, timers, and I/O — all while remaining **single-threaded**.

Understanding the Event Loop is crucial for mastering **performance, async behavior, and debugging timing issues**.

---

## 🧠 Table of Contents

1. [JavaScript Execution Model](#javascript-execution-model)
2. [What is the Event Loop?](#what-is-the-event-loop)
3. [Call Stack](#call-stack)
4. [Heap](#heap)
5. [Queues](#queues)

   * Callback Queue (Macro-task Queue)
   * Microtask Queue
6. [How the Event Loop Works](#how-the-event-loop-works)
7. [Macro-tasks vs Micro-tasks](#macro-tasks-vs-micro-tasks)
8. [Examples: Execution Order](#examples-execution-order)
9. [Promises, async/await, and the Event Loop](#promises-asyncawait-and-the-event-loop)
10. [Visual Representation](#visual-representation)
11. [Common Pitfalls](#common-pitfalls)
12. [Practical Example: Event Loop in Action](#practical-example-event-loop-in-action)
13. [Summary Table](#summary-table)

---

## 🔹 JavaScript Execution Model

JavaScript is **single-threaded**, meaning:

* It executes one piece of code at a time.
* Uses a **Call Stack** to manage execution.
* Handles asynchronous tasks using the **Event Loop**.

---

## 🔹 What is the Event Loop?

The Event Loop is a **mechanism** that allows JavaScript to perform **non-blocking** operations by offloading tasks to the browser or Node.js APIs.

It:

1. Executes synchronous code first.
2. Handles asynchronous tasks (callbacks, promises) later in the proper order.

---

## 🔹 Call Stack

The **Call Stack** stores functions currently being executed.

```js
function first() {
  second();
}
function second() {
  console.log('Inside second');
}
first();
```

**Call Stack:**

```
1. first() pushed
2. second() pushed
3. console.log() executed
4. second() popped
5. first() popped
```

---

## 🔹 Heap

The **Heap** is where objects, arrays, and functions are stored in memory.

---

## 🔹 Queues

JavaScript uses two main queues:

### 1. Callback Queue (Macro-task Queue)

Contains tasks like:

* `setTimeout`
* `setInterval`
* `setImmediate` (Node.js)
* I/O callbacks

### 2. Microtask Queue

Contains tasks like:

* `Promise.then` / `catch` / `finally`
* `process.nextTick` (Node.js)
* `MutationObserver` (Browser)

---

## 🔹 How the Event Loop Works

1. Executes all **synchronous code** (Call Stack).
2. If stack is empty, it checks **Microtask Queue**.
3. Executes **all microtasks** before moving to macro-tasks.
4. Then picks a **macro-task** (like setTimeout) from the Callback Queue.
5. Repeats the cycle.

---

## 🔹 Macro-tasks vs Micro-tasks

| Task Type       | Examples                                             | Executed When                   |
| --------------- | ---------------------------------------------------- | ------------------------------- |
| **Macro-tasks** | `setTimeout`, `setInterval`, I/O                     | After microtasks are cleared    |
| **Micro-tasks** | `Promise.then`, `queueMicrotask`, `process.nextTick` | Immediately after current stack |

### Example

```js
console.log('Start');
setTimeout(() => console.log('Timeout'), 0);
Promise.resolve().then(() => console.log('Promise'));
console.log('End');

// Output:
// Start
// End
// Promise
// Timeout
```

**Why?** Because `Promise.then` (microtask) executes before `setTimeout` (macrotask).

---

## 🔹 Promises, async/await, and the Event Loop

### Promises

Promises create **microtasks** that execute **after** the current synchronous code.

```js
console.log('1');
Promise.resolve().then(() => console.log('2'));
console.log('3');

// 1 → 3 → 2
```

### Async/Await

`await` pauses execution in the async function until the promise resolves — other tasks continue.

```js
async function example() {
  console.log('A');
  await Promise.resolve();
  console.log('B');
}
example();
console.log('C');

// A → C → B
```

---

## 🔹 Visual Representation

### 🔸 Simplified Diagram

```
        ┌──────────────────────┐
        │   Web APIs / Node APIs │
        └──────────┬───────────┘
                   │
┌─────────────┐    │    ┌──────────────┐
│  Call Stack │ ←──┘ ←──│ Event Loop   │
└─────────────┘         └──────────────┘
       ↑                       ↓
┌──────────────┐        ┌─────────────────┐
│ Microtask Q  │        │  Callback Q     │
│ (Promises)   │        │ (setTimeout...) │
└──────────────┘        └─────────────────┘
```

**Execution Order:**
1️⃣ Call Stack → 2️⃣ Microtask Queue → 3️⃣ Callback Queue

---

## 🔹 Common Pitfalls

### 1. Misunderstanding execution order

```js
setTimeout(() => console.log('timeout'), 0);
Promise.resolve().then(() => console.log('promise'));
console.log('sync');
// sync → promise → timeout
```

### 2. Heavy computation blocking the Event Loop

```js
console.log('Start');
for (let i = 0; i < 1e9; i++) {} // Blocks everything
console.log('End');
```

The loop blocks all async tasks until finished.

✅ Fix: Use Web Workers (browser) or child processes (Node.js) for CPU-heavy tasks.

### 3. Starving the Event Loop with endless microtasks

```js
function repeat() {
  Promise.resolve().then(repeat);
}
repeat(); // Freezes app (infinite microtasks)
```

---

## 🔹 Practical Example: Event Loop in Action

```js
console.log('A');
setTimeout(() => console.log('B'), 0);
Promise.resolve().then(() => console.log('C'));
(async () => {
  console.log('D');
  await null;
  console.log('E');
})();
console.log('F');

// Output:
// A
// D
// F
// C
// E
// B
```

**Explanation:**

1. `A`, `D`, `F` are synchronous → executed first.
2. `Promise.then` (C) and `await` continuation (E) → microtasks.
3. `setTimeout` (B) → macro-task.

---

## 🔹 Summary Table

| Concept          | Description                      | Examples                         |
| ---------------- | -------------------------------- | -------------------------------- |
| Call Stack       | Executes functions sequentially  | `function calls`                 |
| Heap             | Memory for objects/functions     | JS runtime memory                |
| Event Loop       | Coordinates async execution      | Moves tasks between stack/queues |
| Microtask Queue  | High-priority async tasks        | `Promise.then`, `await`          |
| Macro-task Queue | Timers, I/O, events              | `setTimeout`, `setInterval`      |
| Execution Order  | Stack → Microtasks → Macrotasks  | `Promise` before `setTimeout`    |
| Blocking         | Long sync tasks delay async ones | Avoid large loops                |

---

### ✅ In Short

* JavaScript runs in a **single thread**.
* **Event Loop** allows non-blocking async execution.
* **Microtasks (Promises)** run **before** macrotasks (`setTimeout`).
* Avoid blocking the loop — it freezes the entire app.

---

**Next Suggested Topic:** 🧩 *JavaScript Modules (ES Modules & CommonJS) — Complete Reference with Examples*
