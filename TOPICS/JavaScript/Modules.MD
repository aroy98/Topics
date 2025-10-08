# JavaScript Modules (ES Modules & CommonJS) — Complete Reference with Examples

Modules allow you to **organize**, **reuse**, and **encapsulate** JavaScript code into separate files. Modern applications rely heavily on modular architecture for maintainability, scalability, and performance.

---

## 🧠 Table of Contents

1. [What Are Modules?](#what-are-modules)
2. [Why Use Modules?](#why-use-modules)
3. [Types of JavaScript Modules](#types-of-javascript-modules)
4. [CommonJS (Node.js)](#commonjs-nodejs)
5. [ES Modules (Browser & Node.js)](#es-modules-browser--nodejs)
6. [Named Exports](#named-exports)
7. [Default Exports](#default-exports)
8. [Import Syntax Variations](#import-syntax-variations)
9. [Dynamic Imports (`import()`)](#dynamic-imports-import)
10. [Module Resolution](#module-resolution)
11. [Interoperability: CommonJS ↔ ES Modules](#interoperability-commonjs--es-modules)
12. [Tree Shaking and Bundling](#tree-shaking-and-bundling)
13. [Modules in Browser vs Node.js](#modules-in-browser-vs-nodejs)
14. [Practical Project Example](#practical-project-example)
15. [Summary Table](#summary-table)

---

## 🔹 What Are Modules?

A **module** is a file that contains reusable JavaScript code. Each module can **export** variables, functions, or classes and **import** them from other modules.

```js
// math.js
export function add(a, b) {
  return a + b;
}

// app.js
import { add } from './math.js';
console.log(add(2, 3)); // 5
```

---

## 🔹 Why Use Modules?

✅ **Encapsulation:** Keep logic isolated per file.
✅ **Reusability:** Import functionality anywhere.
✅ **Maintainability:** Organize code into logical units.
✅ **Scoping:** Avoid global namespace pollution.

---

## 🔹 Types of JavaScript Modules

| Type                 | Environment             | Syntax                         | Example              |
| -------------------- | ----------------------- | ------------------------------ | -------------------- |
| **CommonJS (CJS)**   | Node.js (pre-ESM)       | `require()` / `module.exports` | Legacy Node.js style |
| **ES Modules (ESM)** | Browser, modern Node.js | `import` / `export`            | Modern standard      |

---

## 🔹 CommonJS (Node.js)

CommonJS was the original Node.js module system.

### Exporting

```js
// math.js
function add(a, b) {
  return a + b;
}

function subtract(a, b) {
  return a - b;
}

module.exports = { add, subtract };
```

### Importing

```js
// app.js
const math = require('./math');
console.log(math.add(2, 3)); // 5
```

### Individual Exports

```js
exports.multiply = (a, b) => a * b;
exports.divide = (a, b) => a / b;
```

### Characteristics

* Synchronous (blocks during load)
* Only works in Node.js (not in browsers)
* Uses `require`, `module.exports`

---

## 🔹 ES Modules (Browser & Node.js)

ESM (introduced in ES6) is now the **official JavaScript module system**.

### Enable in Node.js

In your `package.json`:

```json
{
  "type": "module"
}
```

### Exporting

```js
// math.js
export function add(a, b) {
  return a + b;
}
export const PI = 3.14;
```

### Importing

```js
import { add, PI } from './math.js';
console.log(add(2, 3));
console.log(PI);
```

---

## 🔹 Named Exports

You can export multiple things from one module.

```js
// utils.js
export const greet = (name) => `Hello ${name}`;
export const age = 25;
```

Import them using destructuring syntax:

```js
import { greet, age } from './utils.js';
console.log(greet('John')); // Hello John
```

Rename on import:

```js
import { greet as sayHello } from './utils.js';
sayHello('Alice');
```

---

## 🔹 Default Exports

Used when you want to export **one main value** per module.

```js
// logger.js
export default function log(message) {
  console.log(`[LOG]: ${message}`);
}
```

Importing default exports:

```js
import log from './logger.js';
log('Hello World');
```

You can combine both default and named exports:

```js
export default function greet() {}
export const name = 'ModuleExample';
```

---

## 🔹 Import Syntax Variations

| Syntax                                | Description                      |
| ------------------------------------- | -------------------------------- |
| `import defaultExport from 'module'`  | Import default export            |
| `import * as name from 'module'`      | Import all exports as an object  |
| `import { a, b } from 'module'`       | Import specific named exports    |
| `import { a as alias } from 'module'` | Rename import                    |
| `import 'module'`                     | Only execute module (no imports) |

Example:

```js
import * as math from './math.js';
console.log(math.add(2, 2));
```

---

## 🔹 Dynamic Imports (`import()`)

Dynamic import loads modules **on demand**, returning a Promise.

```js
async function loadModule() {
  const math = await import('./math.js');
  console.log(math.add(2, 5));
}
loadModule();
```

✅ Great for lazy loading large files.

---

## 🔹 Module Resolution

### 1. File Extension

Always include `.js` when using ESM in browsers:

```js
import { add } from './math.js';
```

### 2. Relative vs Absolute Imports

```js
import helper from '../utils/helper.js'; // relative
import config from '/config.js';          // absolute (from root)
```

### 3. Node.js Resolution

Node.js searches:

```
./file.js → ./file/index.js → node_modules
```

---

## 🔹 Interoperability: CommonJS ↔ ES Modules

### Import CommonJS in ES Module

```js
import pkg from './commonjs-module.cjs';
console.log(pkg);
```

### Import ES Module in CommonJS

```js
(async () => {
  const { add } = await import('./esm-module.js');
  console.log(add(1, 2));
})();
```

---

## 🔹 Tree Shaking and Bundling

### Tree Shaking

Removes unused exports in bundlers (Webpack, Rollup, Vite) — works only with **ES Modules**.

```js
// math.js
export const add = (a, b) => a + b;
export const unused = () => 'not used';

// app.js
import { add } from './math.js'; // unused() removed during build
```

### Bundlers (Vite, Rollup, Webpack)

These combine multiple files into optimized bundles.

```bash
vite build
# or
webpack --mode production
```

They support:

* Tree shaking
* Code splitting
* Lazy loading (via dynamic imports)

---

## 🔹 Modules in Browser vs Node.js

| Feature        | Browser                        | Node.js                  |
| -------------- | ------------------------------ | ------------------------ |
| Module Type    | ESM only                       | ESM + CommonJS           |
| Syntax         | `import/export`                | `require/module.exports` |
| File Extension | Must include `.js`             | Optional in CJS          |
| Execution      | Runs in strict mode by default | Same for ESM             |
| Dynamic Import | Supported                      | Supported                |

### Browser Example

```html
<script type="module">
  import { greet } from './utils.js';
  greet('Browser');
</script>
```

---

## 🔹 Practical Project Example

### File Structure

```
project/
├── main.js
├── math.js
├── logger.js
```

### math.js

```js
export function add(a, b) {
  return a + b;
}
export function multiply(a, b) {
  return a * b;
}
```

### logger.js

```js
export default function log(msg) {
  console.log(`[LOG] ${msg}`);
}
```

### main.js

```js
import log from './logger.js';
import { add, multiply } from './math.js';

log('Starting calculations...');
console.log(add(2, 3));
console.log(multiply(3, 4));
```

Run using:

```bash
node main.js
```

---

## 🔹 Summary Table

| Concept        | Description              | Example                        |
| -------------- | ------------------------ | ------------------------------ |
| CommonJS       | Old Node.js format       | `require()` / `module.exports` |
| ESM            | Modern standard          | `import` / `export`            |
| Named Export   | Export multiple items    | `export { a, b }`              |
| Default Export | Single export per module | `export default fn`            |
| Dynamic Import | Load module at runtime   | `import('./file.js')`          |
| Tree Shaking   | Removes unused exports   | Bundlers like Rollup/Vite      |
| Interop        | Mix ESM & CJS            | `await import()` in CJS        |

---

### ✅ In Short

* Use **ES Modules** (`import`/`export`) — the future-proof standard.
* **CommonJS** (`require`) is still used in legacy Node.js.
* Use **dynamic imports** for lazy loading.
* Bundlers like **Vite**, **Webpack**, and **Rollup** optimize and tree-shake ESM.

---

**Next Suggested Topic:** ⚡ *JavaScript Event Handling & DOM Events — Complete Reference with Examples*
