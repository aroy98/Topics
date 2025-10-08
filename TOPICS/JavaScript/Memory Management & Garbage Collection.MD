# JavaScript Memory Management & Garbage Collection — Complete Reference with Examples

Memory management in JavaScript is largely **automatic**, thanks to the **Garbage Collector (GC)** in the **V8 engine** (used by Chrome and Node.js). However, understanding how memory is allocated, used, and reclaimed is essential for optimizing performance and preventing memory leaks.

---

## 🧠 Table of Contents

1. [What Is Memory Management?](#what-is-memory-management)
2. [JavaScript Memory Model](#javascript-memory-model)
3. [Stack vs Heap](#stack-vs-heap)
4. [Scopes and Closures](#scopes-and-closures)
5. [Garbage Collection Basics](#garbage-collection-basics)
6. [Mark-and-Sweep Algorithm](#mark-and-sweep-algorithm)
7. [Generational & Incremental GC](#generational--incremental-gc)
8. [Memory Leaks](#memory-leaks)
9. [Detecting Leaks in Browser](#detecting-leaks-in-browser)
10. [WeakMap, WeakSet & WeakRef](#weakmap-weakset--weakref)
11. [Node.js Memory Management](#nodejs-memory-management)
12. [Memory Profiling in Node.js](#memory-profiling-in-nodejs)
13. [Optimization Techniques](#optimization-techniques)
14. [Visual Memory Map](#visual-memory-map)
15. [Summary Table](#summary-table)

---

## 🔹 What Is Memory Management?

Memory management refers to how JavaScript **allocates**, **uses**, and **frees** memory during execution. JavaScript engines like **V8** handle this automatically, but developers can inadvertently create **memory leaks**.

---

## 🔹 JavaScript Memory Model

Memory is divided into two major regions:

* **Stack** → Stores primitive values and function call frames.
* **Heap** → Stores objects, arrays, and functions.

---

## 🔹 Stack vs Heap

### Stack (Static Memory)

* Fast access
* Fixed size (for function calls, primitives)

### Heap (Dynamic Memory)

* Large, flexible
* Used for objects, closures, arrays

### Example

```js
function add(a, b) {
  const result = a + b; // Stored in stack
  const obj = { sum: result }; // Stored in heap
  return obj;
}
```

---

## 🔹 Scopes and Closures

Closures keep references to their outer variables — preventing them from being garbage-collected.

```js
function outer() {
  let count = 0;
  return function inner() {
    count++; // 'count' stays in memory
    console.log(count);
  };
}

const counter = outer();
counter();
```

⚠️ If closures reference large objects, they can cause **memory retention**.

---

## 🔹 Garbage Collection Basics

Garbage collection is **automatic** but not instantaneous.
The GC removes objects **no longer reachable** from the root (`window` in browser, `global` in Node.js).

```js
let user = { name: 'Alice' };
user = null; // Object is unreachable → eligible for GC
```

---

## 🔹 Mark-and-Sweep Algorithm

Most engines (like V8) use **Mark-and-Sweep** GC:

1. **Mark phase** — Traverse object graph from roots (global objects).
2. **Sweep phase** — Delete all unmarked (unreachable) objects.

### Diagram

```
[Root] → [Object A] → [Object B]
           ↓
        [Object C]

If A loses reference to B → B and C become unreachable → GC removes them.
```

---

## 🔹 Generational & Incremental GC

Modern GC optimizes performance via multiple techniques:

### 🧩 Generational GC

* Divides objects into **young** (new) and **old** (long-lived) generations.
* Young objects are collected frequently.
* Old objects are collected less often.

### 🔁 Incremental GC

* GC runs in **small steps** to avoid blocking execution.

### 🧵 Concurrent GC

* Runs in a separate thread in V8 — minimal pause time.

---

## 🔹 Memory Leaks

A **memory leak** occurs when unreachable objects remain referenced and never freed.

### Common Causes

1. **Global variables**

   ```js
   leaked = []; // Implicit global
   ```
2. **Forgotten timers/listeners**

   ```js
   setInterval(() => console.log('leak'), 1000); // Never cleared
   ```
3. **Closures retaining references**

   ```js
   function createLeak() {
     const large = new Array(100000).fill('*');
     return () => console.log(large.length);
   }
   ```
4. **Detached DOM elements**

   ```js
   const div = document.createElement('div');
   document.body.appendChild(div);
   div.remove(); // Still referenced → memory leak
   ```

---

## 🔹 Detecting Leaks in Browser

Use **Chrome DevTools**:

1. Open **Performance → Memory tab**.
2. Take heap snapshots.
3. Compare retained objects before and after.

### Tools

* **Performance Monitor** — real-time JS memory usage.
* **Lighthouse** — audits for leaks.

---

## 🔹 WeakMap, WeakSet & WeakRef

### WeakMap

* Keys are **weakly referenced** → if no other reference, they’re GC’d.

```js
let obj = { name: 'Alice' };
const weakMap = new WeakMap();
weakMap.set(obj, 'metadata');
obj = null; // Automatically garbage collected
```

### WeakSet

* Works like WeakMap but only holds objects.

### WeakRef (ES2021)

* Allows weak reference to object without preventing GC.

```js
let user = { name: 'Bob' };
const weak = new WeakRef(user);
user = null;
console.log(weak.deref()); // May return undefined after GC
```

---

## 🔹 Node.js Memory Management

### V8 Heap Structure

* **New Space (Young Gen)** — small, frequent GC.
* **Old Space (Old Gen)** — long-lived objects.
* **Large Object Space** — huge objects.

### Check Memory Usage

```js
console.log(process.memoryUsage());
```

Example Output:

```js
{
  rss: 23592960,
  heapTotal: 6586368,
  heapUsed: 4576208,
  external: 105472
}
```

### Increase Memory Limit

Default Node.js limit ≈ 2 GB. To increase:

```bash
node --max-old-space-size=4096 app.js
```

---

## 🔹 Memory Profiling in Node.js

### 1. Using Chrome DevTools

Run Node with inspect flag:

```bash
node --inspect-brk app.js
```

Open **chrome://inspect** → Take snapshots.

### 2. Using Built-in Inspector

```js
const inspector = require('inspector');
inspector.open(9229, null, true);
```

### 3. Monitoring

```js
setInterval(() => {
  const { heapUsed, heapTotal } = process.memoryUsage();
  console.log(`Heap used: ${(heapUsed / 1024 / 1024).toFixed(2)} MB`);
}, 3000);
```

---

## 🔹 Optimization Techniques

✅ **Nullify references** when no longer needed:

```js
obj = null;
```

✅ **Use WeakMap/WeakSet** for ephemeral data.
✅ **Unsubscribe** event listeners.
✅ **Throttle/debounce** DOM events.
✅ Avoid **storing DOM nodes** in closures.
✅ Keep functions short-lived.

---

## 🔹 Visual Memory Map

```
      ┌───────────────────────────────┐
      │          Call Stack          │
      │ ─────────────────────────── │
      │ add() → local vars          │
      │ compute() → primitives      │
      └──────────────┬────────────────┘
                     │ references
                     ▼
      ┌───────────────────────────────┐
      │             Heap              │
      │ ─────────────────────────── │
      │ Object: { name: 'Alice' }   │
      │ Array: [1,2,3]              │
      │ Closure: { counter: 5 }     │
      └───────────────────────────────┘

   GC Root → Stack → Heap objects → Mark & Sweep unreferenced
```

---

## 🔹 Summary Table

| Concept         | Description              | Example                   |
| --------------- | ------------------------ | ------------------------- |
| Stack           | Fast, stores primitives  | `let x = 10;`             |
| Heap            | Dynamic, stores objects  | `{ key: 'value' }`        |
| GC              | Automatic cleanup        | Mark-and-sweep            |
| Generational GC | Divides objects by age   | Young/Old spaces          |
| Memory Leak     | Unreleased references    | Event listeners, closures |
| WeakMap         | GC-safe key-value store  | `weakMap.set(obj, data)`  |
| WeakRef         | Weak reference to object | `new WeakRef(obj)`        |
| DevTools        | Browser memory profiling | Heap snapshots            |
| Node Flags      | Increase memory          | `--max-old-space-size`    |

---

### ✅ In Short

* JS memory is divided into **stack** (primitives) and **heap** (objects).
* **Garbage collection** is automatic but depends on reachability.
* Use **WeakMap/WeakRef** to avoid leaks.
* Always remove **event listeners** and **intervals**.
* Monitor memory using **DevTools** or **process.memoryUsage()** in Node.js.

---

**Next Suggested Topic:** 🧩 *JavaScript Error Handling, Debugging, and Performance Optimization — Complete Reference with Examples*
