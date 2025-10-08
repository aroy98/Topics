# JavaScript Promises & Async/Await — Complete Reference with Examples

Modern JavaScript uses **Promises** and **async/await** to handle asynchronous operations cleanly — without getting stuck in callback hell.

---

## ⚙️ Table of Contents

1. [What is Asynchronous JavaScript?](#what-is-asynchronous-javascript)
2. [What is a Promise?](#what-is-a-promise)
3. [Promise States](#promise-states)
4. [Creating a Promise](#creating-a-promise)
5. [Consuming Promises (`then`, `catch`, `finally`)](#consuming-promises-then-catch-finally)
6. [Chaining Promises](#chaining-promises)
7. [Promise.all, Promise.race, Promise.allSettled, Promise.any](#promise-utility-methods)
8. [Async/Await Syntax](#asyncawait-syntax)
9. [Error Handling in Async/Await](#error-handling-in-asyncawait)
10. [Parallel Execution with Async/Await](#parallel-execution-with-asyncawait)
11. [Converting Callbacks to Promises](#converting-callbacks-to-promises)
12. [Common Mistakes](#common-mistakes)
13. [Summary Table](#summary-table)

---

## 🔹 What is Asynchronous JavaScript?

JavaScript is **single-threaded** — it executes one task at a time. Asynchronous programming allows non-blocking operations like:

* Fetching data from an API
* Reading files
* Delayed execution (setTimeout)

```js
console.log('Start');
setTimeout(() => console.log('Async Task Complete'), 2000);
console.log('End');

// Output:
// Start
// End
// Async Task Complete
```

---

## 🔹 What is a Promise?

A **Promise** is an object representing the eventual completion or failure of an asynchronous operation.

```js
const promise = new Promise((resolve, reject) => {
  const success = true;
  if (success) resolve('Data fetched!');
  else reject('Error fetching data');
});
```

---

## 🔹 Promise States

A promise can be in one of three states:

| State       | Meaning                          |
| ----------- | -------------------------------- |
| `pending`   | Operation not completed yet      |
| `fulfilled` | Operation completed successfully |
| `rejected`  | Operation failed                 |

---

## 🔹 Creating a Promise

```js
function fetchData() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('Data received');
    }, 2000);
  });
}

fetchData().then(data => console.log(data));
// Data received (after 2s)
```

---

## 🔹 Consuming Promises (`then`, `catch`, `finally`)

```js
fetch('https://jsonplaceholder.typicode.com/todos/1')
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('Error:', error))
  .finally(() => console.log('Request completed'));
```

### `then()`

Handles fulfilled promises.

### `catch()`

Handles rejections or errors.

### `finally()`

Executes after promise settles (fulfilled or rejected).

---

## 🔹 Chaining Promises

```js
getUser()
  .then(user => getPosts(user.id))
  .then(posts => getComments(posts[0].id))
  .then(comments => console.log(comments))
  .catch(err => console.error(err));
```

Each `.then()` returns a **new promise**, enabling chaining.

---

## 🔹 Promise Utility Methods

### `Promise.all()` — Run multiple promises in parallel

```js
Promise.all([
  fetch('/api/user'),
  fetch('/api/posts')
]).then(results => console.log('All done'));
```

Fails if any promise rejects.

### `Promise.allSettled()` — Wait for all, regardless of success/failure

```js
Promise.allSettled([
  Promise.resolve('✅'),
  Promise.reject('❌')
]).then(results => console.log(results));
```

### `Promise.race()` — Returns first settled promise

```js
Promise.race([
  fetch('/api/fast'),
  fetch('/api/slow')
]).then(result => console.log('Fastest wins:', result));
```

### `Promise.any()` — Returns first *fulfilled* promise (ignores rejections)

```js
Promise.any([
  Promise.reject('Fail 1'),
  Promise.resolve('Success!')
]).then(console.log); // Success!
```

---

## 🔹 Async/Await Syntax

`async/await` provides a cleaner way to write asynchronous code that looks synchronous.

### Example

```js
async function fetchData() {
  console.log('Fetching...');
  const response = await fetch('https://jsonplaceholder.typicode.com/todos/1');
  const data = await response.json();
  console.log(data);
}
fetchData();
```

### How it Works

* Functions marked `async` always return a **Promise**.
* `await` pauses execution until the promise settles.

---

## 🔹 Error Handling in Async/Await

Use `try...catch` to handle errors.

```js
async function getUserData() {
  try {
    const response = await fetch('https://api.fake.com/user');
    const data = await response.json();
    console.log(data);
  } catch (error) {
    console.error('Fetch failed:', error);
  } finally {
    console.log('Done');
  }
}
```

---

## 🔹 Parallel Execution with Async/Await

Instead of waiting sequentially, use `Promise.all` for parallel tasks.

```js
async function getAllData() {
  const [user, posts] = await Promise.all([
    fetch('/api/user').then(r => r.json()),
    fetch('/api/posts').then(r => r.json())
  ]);
  console.log(user, posts);
}
```

---

## 🔹 Converting Callbacks to Promises

Example: wrapping `setTimeout`.

```js
function delay(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

async function run() {
  console.log('Wait 2 seconds...');
  await delay(2000);
  console.log('Done!');
}
run();
```

---

## 🔹 Common Mistakes

### ❌ Forgetting `await`

```js
async function badFetch() {
  const data = fetch('/api'); // returns Promise, not data
  console.log(data); // Promise { <pending> }
}
```

✅ Fix:

```js
const data = await fetch('/api');
```

### ❌ Mixing `then` with `await`

Avoid mixing chaining styles unnecessarily.

### ❌ Running sequential awaits unnecessarily

```js
// ❌ Slow
await task1();
await task2();

// ✅ Faster
await Promise.all([task1(), task2()]);
```

---

## 🔹 Summary Table

| Concept      | Description                  | Example                |
| ------------ | ---------------------------- | ---------------------- |
| Promise      | Represents async result      | `new Promise()`        |
| States       | pending → fulfilled/rejected | `resolve() / reject()` |
| then/catch   | Handle success/error         | `p.then().catch()`     |
| finally      | Runs regardless of outcome   | `p.finally()`          |
| Promise.all  | Wait all success             | `[p1,p2]`              |
| Promise.race | First settled wins           | `Promise.race()`       |
| Promise.any  | First fulfilled wins         | `Promise.any()`        |
| async/await  | Synchronous-looking async    | `await fetch()`        |
| try...catch  | Error handling for await     | Handle rejections      |

---

### ✅ In Short

* **Promise:** future value placeholder.
* **Async/Await:** syntactic sugar for Promises.
* Always handle rejections with `catch` or `try...catch`.
* Use `Promise.all` for concurrency, not sequential awaits.

---

**Next Suggested Topic:** 🔁 *JavaScript Event Loop — Complete Reference with Examples*
