# Node.js Mastery Series

## **Part I – Fundamentals**

### **1. Node.js Architecture & Event Loop**

* **Single-threaded, non-blocking I/O model** built on **Google’s V8 engine**.
* Uses **libuv** to handle asynchronous operations via **event-driven architecture**.
* **Event Loop Phases:**

  1. **Timers** (setTimeout, setInterval)
  2. **Pending Callbacks**
  3. **Idle, Prepare**
  4. **Poll** (I/O events)
  5. **Check** (setImmediate)
  6. **Close Callbacks**
* Handles concurrency via **callbacks, promises, async/await**.

### **2. Global Objects & Modules**

* Common global objects:

  * `__dirname`, `__filename`
  * `process`, `global`
  * `setTimeout()`, `setInterval()`, `clearTimeout()`, `console`
* **Built-in Modules:** `fs`, `path`, `os`, `http`, `events`, `stream`
* Custom modules use **module.exports** and **require()**.

### **3. CommonJS vs ES Modules**

| Feature        | CommonJS         | ES Modules                         |
| -------------- | ---------------- | ---------------------------------- |
| Syntax         | `require()`      | `import`                           |
| Exports        | `module.exports` | `export` / `export default`        |
| File Extension | `.js`            | `.mjs` (or enable in package.json) |
| Loading        | Synchronous      | Asynchronous                       |

Example:

```js
// CommonJS
const fs = require('fs');
module.exports = myFunction;

// ES Module
import fs from 'fs';
export default myFunction;
```

### **4. File System (fs)**

* **Synchronous:** Blocks execution

  ```js
  const data = fs.readFileSync('file.txt', 'utf8');
  ```
* **Asynchronous:** Non-blocking

  ```js
  fs.readFile('file.txt', 'utf8', (err, data) => {
    if (err) throw err;
    console.log(data);
  });
  ```
* **Promises API:**

  ```js
  import { promises as fs } from 'fs';
  const data = await fs.readFile('file.txt', 'utf8');
  ```

### **5. Streams and Buffers**

* **Stream Types:** Readable, Writable, Duplex, Transform.
* Efficient for large data transfers.
* Example:

  ```js
  const fs = require('fs');
  const stream = fs.createReadStream('large.txt');
  stream.on('data', chunk => console.log(chunk));
  ```
* **Buffer:** Temporary binary data storage.

  ```js
  const buf = Buffer.from('Node.js');
  console.log(buf.toString()); // 'Node.js'
  ```

### **6. Events and Emitters**

* Use **EventEmitter** from the `events` module.

  ```js
  const EventEmitter = require('events');
  const emitter = new EventEmitter();

  emitter.on('message', (data) => console.log('Received:', data));
  emitter.emit('message', 'Hello Node.js');
  ```

---

## **Part II – Asynchronous Programming**

### **1. Callbacks, Promises, Async/Await**

* **Callback Hell:** Nested callbacks → difficult to maintain.
* **Promises:**

  ```js
  fetchData()
    .then(res => process(res))
    .catch(err => console.error(err));
  ```
* **Async/Await:**

  ```js
  async function getData() {
    try {
      const res = await fetchData();
      console.log(res);
    } catch (err) {
      console.error(err);
    }
  }
  ```

### **2. Event-Driven Architecture**

* Core of Node.js.
* Uses **event emitters**, **listeners**, and **callbacks**.

### **3. Error Handling**

* **Callback style:** `(err, result)` pattern.
* **Try/Catch:** For promises and async/await.

  ```js
  try { await something(); } catch (e) { console.error(e); }
  ```

### **4. Worker Threads & Clustering**

* **Worker Threads:** Parallel CPU-bound tasks.
* **Cluster:** Utilize multi-core systems.

  ```js
  const cluster = require('cluster');
  if (cluster.isPrimary) {
    cluster.fork();
  } else {
    require('./server');
  }
  ```

---

## **Part III – Building APIs**

### **1. Setting Up Express Server**

```js
const express = require('express');
const app = express();
app.listen(3000, () => console.log('Server running...'));
```

### **2. Middleware System**

```js
app.use(express.json());
app.use((req, res, next) => {
  console.log(`${req.method} ${req.url}`);
  next();
});
```

### **3. Routing**

```js
app.get('/users', (req, res) => res.json(users));
```

### **4. RESTful API Design**

* Use nouns for endpoints.
* HTTP verbs: `GET`, `POST`, `PUT`, `DELETE`.

### **5. Request Validation**

```js
import { body, validationResult } from 'express-validator';

app.post('/user', [body('email').isEmail()], (req, res) => {
  const errors = validationResult(req);
  if (!errors.isEmpty()) return res.status(400).json({ errors: errors.array() });
  res.send('Valid');
});
```

### **6. Error Handling Middleware**

```js
app.use((err, req, res, next) => {
  console.error(err);
  res.status(500).send('Server Error');
});
```

---

## **Part IV – Databases**

### **1. Connecting with MongoDB, PostgreSQL, SQLite**

* **MongoDB (Mongoose):**

  ```js
  import mongoose from 'mongoose';
  mongoose.connect('mongodb://localhost/test');
  ```
* **PostgreSQL (pg):**

  ```js
  import { Pool } from 'pg';
  const pool = new Pool({ user: 'user', host: 'localhost' });
  ```
* **SQLite (better-sqlite3):**

  ```js
  const db = require('better-sqlite3')('data.db');
  ```

### **2. ORM/ODM**

* **Prisma:** Type-safe database access.
* **Sequelize:** SQL ORM.
* **Mongoose:** ODM for MongoDB.

### **3. Query Optimization**

* Use **indexes**, **projection**, and **pagination**.

### **4. Transactions and Indexing**

* Maintain data integrity using transactions.

---

## **Part V – Authentication & Authorization**

### **1. JWT, OAuth2, Session-Based Auth**

```js
import jwt from 'jsonwebtoken';
const token = jwt.sign({ id: user.id }, process.env.JWT_SECRET);
```

### **2. Password Hashing**

```js
import bcrypt from 'bcryptjs';
const hash = await bcrypt.hash(password, 10);
```

### **3. Role-Based Access Control (RBAC)**

* Store user roles and permissions in DB.

### **4. Integration with Auth0 / Firebase Auth**

* Use SDKs or JWTs for authentication flow.

---

## **Part VI – Testing & CI/CD**

### **1. Unit Testing (Jest, Mocha)**

```js
test('adds 1 + 2 to equal 3', () => {
  expect(1 + 2).toBe(3);
});
```

### **2. Integration Tests**

* Use **Supertest** for API testing.

### **3. Test Database Setup**

* Use in-memory DB (like MongoMemoryServer).

### **4. CI/CD with GitHub Actions or Jenkins**

* Automate build, test, and deploy pipelines.

---

## **Part VII – Advanced Node Concepts**

### **1. Streams in Depth**

* Use **pipe()** for chaining streams.

### **2. Child Processes**

```js
const { exec } = require('child_process');
exec('ls', (err, stdout) => console.log(stdout));
```

### **3. Clustering**

* Scale across CPU cores.

### **4. Performance Tuning**

* Use **profiler**, **PM2**, and **Node Inspector**.

### **5. Node.js Internals (libuv, v8)**

* **libuv:** Manages thread pool & async I/O.
* **V8:** Compiles JS to machine code.

---

## **Part VIII – Production & Deployment**

### **1. Environment Variables**

```js
import dotenv from 'dotenv';
dotenv.config();
```

### **2. Logging & Monitoring**

* **Winston**, **Morgan**, **Sentry** for logs.

### **3. PM2 & Dockerization**

* PM2 for process management.
* Docker for containerization.

### **4. Scaling with Kubernetes**

* Auto-scale Node.js apps using deployments.

### **5. Security & Best Practices**

* Use **helmet**, **rate-limiter**, and **input sanitization**.

---

> **Next Up:** Part II – Asynchronous Programming (Deep Dive)
