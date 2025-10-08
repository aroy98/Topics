# Node.js Mastery Series

## **Part VII – Advanced Node Concepts**

### **1. Streams in Depth**

Streams are core to Node.js for efficient data handling, especially with large files or continuous data flows.

**Types of Streams:**

* **Readable** – For reading data (e.g., fs.createReadStream)
* **Writable** – For writing data (e.g., fs.createWriteStream)
* **Duplex** – Both readable and writable (e.g., TCP sockets)
* **Transform** – Modify data during streaming (e.g., compression)

#### **Example: File Copy Using Streams**

```js
import fs from 'fs';

const readStream = fs.createReadStream('input.txt');
const writeStream = fs.createWriteStream('output.txt');
readStream.pipe(writeStream);
```

#### **Transform Stream Example:**

```js
import { Transform } from 'stream';
const upperCaseTransform = new Transform({
  transform(chunk, encoding, callback) {
    callback(null, chunk.toString().toUpperCase());
  }
});
process.stdin.pipe(upperCaseTransform).pipe(process.stdout);
```

---

### **2. Child Processes**

Use `child_process` to run external commands or create subprocesses.

#### **Example: exec()**

```js
import { exec } from 'child_process';

exec('ls -la', (error, stdout, stderr) => {
  if (error) console.error(`Error: ${error.message}`);
  console.log(`Output: ${stdout}`);
});
```

#### **Example: spawn()**

```js
import { spawn } from 'child_process';
const child = spawn('node', ['-v']);

child.stdout.on('data', data => console.log(`Version: ${data}`));
```

#### **Example: fork() for Node scripts**

```js
import { fork } from 'child_process';
const child = fork('worker.js');
child.on('message', msg => console.log('Message from child:', msg));
child.send({ task: 'processData' });
```

---

### **3. Clustering for Multi-Core Scaling**

Node.js runs on a single thread by default. **Clustering** allows utilization of multiple CPU cores.

```js
import cluster from 'cluster';
import os from 'os';
import http from 'http';

if (cluster.isPrimary) {
  const numCPUs = os.cpus().length;
  console.log(`Primary process running with ${numCPUs} workers`);
  for (let i = 0; i < numCPUs; i++) cluster.fork();

  cluster.on('exit', (worker) => {
    console.log(`Worker ${worker.process.pid} died, restarting...`);
    cluster.fork();
  });
} else {
  http.createServer((req, res) => {
    res.writeHead(200);
    res.end(`Response from worker ${process.pid}`);
  }).listen(3000);
}
```

---

### **4. Performance Tuning**

Optimizing Node.js performance involves efficient event handling, memory usage, and asynchronous processing.

#### **Key Strategies:**

* Use **asynchronous APIs** instead of blocking calls.
* Optimize **middleware** order and **route complexity**.
* Use **caching** (Redis, in-memory)
* Monitor **memory leaks** using tools like `clinic.js` or Chrome DevTools.

#### **Example: Profiling Performance**

```bash
node --inspect server.js
```

Open `chrome://inspect` in Chrome DevTools.

#### **Heap Snapshot Example:**

```js
import v8 from 'v8';
import fs from 'fs';
fs.writeFileSync('heap.json', JSON.stringify(v8.getHeapStatistics()));
```

---

### **5. Node.js Internals: libuv, V8, and Event Loop**

Understanding internals helps optimize system-level operations.

#### **V8 Engine:**

* Compiles JavaScript into **machine code**.
* Handles **memory management** and **garbage collection**.

#### **libuv Library:**

* Provides Node.js with **event loop**, **asynchronous I/O**, and **thread pool**.
* Handles filesystem, DNS, and networking in C/C++.

#### **Event Loop Recap:**

* Single-threaded mechanism managing async tasks.
* Divided into multiple **phases** (Timers, Poll, Check, Close, etc.).

---

### **6. Worker Threads (Parallel Execution)**

Used for CPU-intensive tasks that can block the event loop.

```js
import { Worker, isMainThread, parentPort } from 'worker_threads';

if (isMainThread) {
  const worker = new Worker('./worker.js');
  worker.on('message', msg => console.log('Result:', msg));
  worker.postMessage(10);
} else {
  parentPort.on('message', num => {
    parentPort.postMessage(num * 2);
  });
}
```

---

### **7. Load Testing and Benchmarking**

Performance testing ensures your app handles load efficiently.

#### **Tools:**

* **Artillery** – API load testing
* **Autocannon** – Node.js benchmarker
* **k6** – Cloud-native load testing

#### **Example (Autocannon):**

```bash
npx autocannon http://localhost:3000
```

---

### **8. Debugging Techniques**

#### **1. Console Logging:**

Simple but effective.

```js
console.log('Debug:', variable);
```

#### **2. Node Inspector:**

```bash
node --inspect-brk index.js
```

#### **3. VSCode Debugger:**

Use launch configurations for step debugging.

#### **4. Error Stack Analysis:**

```js
process.on('uncaughtException', (err) => console.error('Error:', err));
```

---

### **9. Memory Management**

* Avoid retaining large objects unnecessarily.
* Use **WeakMap/WeakSet** for temporary references.
* Set environment limits:

```bash
node --max-old-space-size=4096 app.js
```

---

### **10. Real-World Example: Multi-Threaded File Processor**

```js
import { Worker } from 'worker_threads';
import fs from 'fs';

const files = ['a.txt', 'b.txt', 'c.txt'];

for (const file of files) {
  const worker = new Worker('./fileWorker.js', { workerData: file });
  worker.on('message', msg => console.log(`Processed ${msg}`));
}
```

**fileWorker.js:**

```js
import { parentPort, workerData } from 'worker_threads';
import fs from 'fs';

const content = fs.readFileSync(workerData, 'utf8').toUpperCase();
fs.writeFileSync(`processed_${workerData}`, content);
parentPort.postMessage(workerData);
```

---

## **Next Up: Part VIII – Production & Deployment**
