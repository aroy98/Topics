# Node.js Mastery Series

## **Part II – Asynchronous Programming (Deep Dive)**

### **1. The Asynchronous Nature of Node.js**

* Node.js runs **non-blocking I/O operations** using an event-driven architecture.
* It executes code on a **single thread**, but can handle **multiple concurrent operations** using **callbacks**, **promises**, or **async/await**.

### **2. The Event Loop in Depth**

* The **event loop** enables asynchronous programming by managing callbacks and background tasks.
* It runs in **phases**, each handling specific types of operations.

**Event Loop Phases:**

1. **Timers Phase** – Executes `setTimeout` and `setInterval` callbacks.
2. **Pending Callbacks Phase** – Executes I/O callbacks deferred from previous cycles.
3. **Idle/Prepare Phase** – Internal use.
4. **Poll Phase** – Retrieves new I/O events; executes I/O callbacks.
5. **Check Phase** – Executes `setImmediate()` callbacks.
6. **Close Callbacks Phase** – Executes `close` event callbacks.

**Microtasks (nextTick, Promises)**

* Microtasks are executed after each phase before the event loop continues.
* Order of priority:

  1. `process.nextTick()`
  2. Promises

Example:

```js
console.log('Start');
setTimeout(() => console.log('setTimeout'), 0);
Promise.resolve().then(() => console.log('Promise'));
process.nextTick(() => console.log('nextTick'));
console.log('End');
```

**Output:**

```
Start
End
nextTick
Promise
setTimeout
```

---

### **3. Callbacks**

* The original Node.js async pattern.
* Pass a callback function as the last argument.

Example:

```js
const fs = require('fs');
fs.readFile('file.txt', 'utf8', (err, data) => {
  if (err) return console.error(err);
  console.log(data);
});
```

**Drawback:** Callback hell (deep nesting).

---

### **4. Promises**

* Introduced to solve callback hell.
* Represents an operation that will complete in the future.

Example:

```js
const readFile = (path) => {
  return new Promise((resolve, reject) => {
    fs.readFile(path, 'utf8', (err, data) => {
      if (err) reject(err);
      else resolve(data);
    });
  });
};

readFile('file.txt')
  .then(data => console.log(data))
  .catch(err => console.error(err));
```

**Chaining Promises:**

```js
fetchData()
  .then(processData)
  .then(saveData)
  .catch(handleError);
```

---

### **5. Async/Await**

* Built on top of Promises.
* Makes async code look synchronous.

Example:

```js
async function fetchUserData() {
  try {
    const data = await getUserFromDB();
    console.log(data);
  } catch (err) {
    console.error(err);
  }
}
```

**Parallel Execution:**

```js
const [user, posts] = await Promise.all([getUser(), getPosts()]);
```

---

### **6. Error Handling in Async Code**

**Callback style:**

```js
fs.readFile('file.txt', (err, data) => {
  if (err) return console.error(err);
});
```

**Promise style:**

```js
readFile('file.txt')
  .then(data => console.log(data))
  .catch(err => console.error('Error:', err));
```

**Async/Await style:**

```js
try {
  const data = await readFile('file.txt');
} catch (err) {
  console.error('Error:', err);
}
```

---

### **7. EventEmitter and Event-Driven Design**

* The **EventEmitter** class is at the heart of Node.js asynchronous patterns.
* Enables communication between modules via events.

Example:

```js
const EventEmitter = require('events');
const emitter = new EventEmitter();

emitter.on('data_received', () => {
  console.log('Data received event triggered!');
});

emitter.emit('data_received');
```

**Best Practices:**

* Always remove listeners with `emitter.removeListener()` or `emitter.off()`.
* Use `once()` for one-time events.

---

### **8. Worker Threads & Clustering**

* Used for **CPU-intensive tasks**.

**Worker Threads:**

```js
import { Worker } from 'worker_threads';

const worker = new Worker('./worker.js', { workerData: 5 });
worker.on('message', msg => console.log('Result:', msg));
```

**worker.js:**

```js
import { parentPort, workerData } from 'worker_threads';
parentPort.postMessage(workerData * 2);
```

**Clustering:**

```js
import cluster from 'cluster';
import os from 'os';

if (cluster.isPrimary) {
  const numCPUs = os.cpus().length;
  for (let i = 0; i < numCPUs; i++) cluster.fork();
  cluster.on('exit', (worker) => console.log(`Worker ${worker.process.pid} exited`));
} else {
  import('./server.js');
}
```

---

### **9. Async Iterators & Generators**

* Used for **streaming data asynchronously**.

Example:

```js
async function* fetchLines(file) {
  const stream = fs.createReadStream(file, { encoding: 'utf8' });
  for await (const chunk of stream) {
    yield chunk;
  }
}

for await (const line of fetchLines('file.txt')) {
  console.log(line);
}
```

---

### **10. Real-World Example: Async Data Pipeline**

```js
import fs from 'fs/promises';

async function processFile() {
  try {
    const data = await fs.readFile('input.txt', 'utf8');
    const processed = data.toUpperCase();
    await fs.writeFile('output.txt', processed);
    console.log('File processed successfully!');
  } catch (err) {
    console.error('Pipeline Error:', err);
  }
}

processFile();
```

---

## **Next Up: Part III – Building APIs**
