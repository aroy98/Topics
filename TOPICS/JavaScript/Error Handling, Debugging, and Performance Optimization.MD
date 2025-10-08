# JavaScript Error Handling, Debugging, and Performance Optimization — Complete Reference with Examples

Errors and performance issues are inevitable in software. Knowing how to **capture**, **diagnose**, and **fix** problems — and how to **profile** and **optimize** performance — is essential for building reliable, fast JavaScript applications.

---

## 🧠 Table of Contents

1. [Error Handling Basics (`try...catch`, `throw`, `finally`)](#error-handling-basics-trycatch-throw-finally)
2. [Custom Error Classes (`extends Error`)](#custom-error-classes-extends-error)
3. [Synchronous vs Asynchronous Errors](#synchronous-vs-asynchronous-errors)
4. [Unhandled Promise Rejections & Best Practices](#unhandled-promise-rejections--best-practices)
5. [Debugging with Chrome DevTools](#debugging-with-chrome-devtools)
6. [Debugging Node.js (Inspector & Tools)](#debugging-nodejs-inspector--tools)
7. [Source Maps & Stack Traces](#source-maps--stack-traces)
8. [Logging Strategies & Observability](#logging-strategies--observability)
9. [Error Reporting & Monitoring (Sentry, LogRocket, etc.)](#error-reporting--monitoring-sentry-logrocket-etc)
10. [Performance Profiling (CPU, Memory, Rendering)](#performance-profiling-cpu-memory-rendering)
11. [Common Performance Anti-patterns](#common-performance-anti-patterns)
12. [Optimization Techniques (Debounce, Throttle, rAF, Web Workers)](#optimization-techniques-debounce-throttle-raf-web-workers)
13. [Rendering Optimizations (Reflows, Repaints, Layout)](#rendering-optimizations-reflows-repaints-layout)
14. [Network Performance (Caching, Compression, HTTP/2)](#network-performance-caching-compression-http2)
15. [Build Optimizations (Tree Shaking, Code Splitting)](#build-optimizations-tree-shaking-code-splitting)
16. [Testing & Regression Detection (Lighthouse, Performance Budgets)](#testing--regression-detection-lighthouse-performance-budgets)
17. [Practical Examples & Recipes](#practical-examples--recipes)
18. [Performance Optimization Flow Diagram](#performance-optimization-flow-diagram)
19. [Summary Table](#summary-table)

---

## 🔹 Error Handling Basics (`try...catch`, `throw`, `finally`)

### `try...catch` Syntax

```js
try {
  // code that may throw
  const result = riskyOperation();
  console.log(result);
} catch (err) {
  console.error('Error caught:', err);
} finally {
  // runs whether error happened or not
  cleanup();
}
```

* Use `try...catch` to handle **synchronous** exceptions.
* `finally` block is useful for resource cleanup (closing files, clearing timers).

### Throwing Errors

```js
function validate(age) {
  if (age < 0) throw new RangeError('Age must be >= 0');
}
```

---

## 🔹 Custom Error Classes (`extends Error`)

Create clear, typed errors for more meaningful stacks and `.name` values.

```js
class ValidationError extends Error {
  constructor(message) {
    super(message);
    this.name = 'ValidationError';
  }
}

throw new ValidationError('Invalid input');
```

Benefits:

* Distinguish error types in `catch` blocks.
* Add custom properties like `statusCode`, `code`, or `meta`.

---

## 🔹 Synchronous vs Asynchronous Errors

* `try...catch` only catches errors thrown **synchronously** inside the `try`.

```js
try {
  setTimeout(() => {
    throw new Error('Async error');
  }, 0);
} catch (err) {
  // ❌ won't catch the async error
}
```

* For async errors, handle via callbacks, Promises, or `async/await`.

```js
// Promise
someAsync().catch(err => console.error(err));

// async/await
try {
  await someAsync();
} catch (err) {
  console.error(err);
}
```

---

## 🔹 Unhandled Promise Rejections & Best Practices

* Node.js emits `unhandledRejection` for unhandled Promise rejections.

```js
process.on('unhandledRejection', (reason, promise) => {
  console.error('Unhandled Rejection:', reason);
  // log and possibly exit
});
```

* Best practices:

  * Always return or `await` Promises in top-level code where possible.
  * Use global handlers to log and fail fast in production.

---

## 🔹 Debugging with Chrome DevTools

### Key Panels

* **Sources** — set breakpoints, step through code.
* **Console** — live logs and REPL.
* **Network** — inspect requests and timing.
* **Performance** — CPU profile and flame charts.
* **Memory** — heap snapshots and allocation timelines.

### Useful Techniques

* **Break on exception**: enable “Pause on exceptions” to break when an error is thrown.
* **Conditional breakpoints**: right-click a line → Add conditional breakpoint.
* **XHR/fetch breakpoints**: pause when specific network calls occur.
* **Blackbox scripts**: ignore library code while stepping.

### Live edit & evaluate

* Edit code in **Sources** and hot-reload functions to test fixes.

---

## 🔹 Debugging Node.js (Inspector & Tools)

### Start Node with Inspector

```bash
node --inspect-brk index.js
```

Open `chrome://inspect` and connect.

### Useful Tools

* **node --inspect** — connect DevTools.
* **ndb** — improved Node debugging UI.
* **clinic.js** — performance and profiling (clinic doctor, flame, bubbleprof).
* **0x** — flamegraphs for Node.

### Inspecting Async Stack Traces

* Use `Error.captureStackTrace` and `async_hooks` for advanced tracing.

---

## 🔹 Source Maps & Stack Traces

* Source maps map compiled code (bundled/minified) back to original sources.
* Generate source maps in build tools (Webpack, Vite) and upload them to monitoring services.

Example `webpack.config.js`:

```js
module.exports = {
  devtool: 'source-map',
};
```

* Stack traces in minified code are hard to read — source maps restore them.

---

## 🔹 Logging Strategies & Observability

* Use structured logs (JSON) for easier processing.
* Include context: request ids, user ids, breadcrumbs.
* Avoid logging sensitive data.

Example with Winston:

```js
const winston = require('winston');
const logger = winston.createLogger({ transports: [new winston.transports.Console()] });
logger.info('User login', { userId: 123 });
```

---

## 🔹 Error Reporting & Monitoring (Sentry, LogRocket, etc.)

* Use tools like **Sentry**, **Rollbar**, **LogRocket**, **Bugsnag** to capture exceptions and performance metrics.
* Attach source maps for readable stack traces.
* Configure sampling and rate limits to control cost.

Example Sentry init:

```js
Sentry.init({ dsn: 'https://...', tracesSampleRate: 0.2 });
Sentry.captureException(err);
```

---

## 🔹 Performance Profiling (CPU, Memory, Rendering)

### CPU Profiling

* Use **Performance** panel in DevTools → record CPU profile → inspect function hot spots and flame charts.
* In Node, use `--prof` or `clinic` to generate profiles.

### Memory Profiling

* Use **Memory** tab → Heap snapshot → Inspect retained tree.
* Detect detached DOM nodes, listeners, closures holding large arrays.

### Rendering Performance

* Use **Rendering** and **Performance** tabs to see paints, layout, and frame rates.
* FPS drops indicate main-thread blocking.

---

## 🔹 Common Performance Anti-patterns

* Blocking the main thread with heavy JS loops.
* Frequent DOM writes without batching.
* Unbounded event listeners (adding per item without delegation).
* Excessive reflows due to layout thrashing.
* Large synchronous parse of JSON on main thread.

---

## 🔹 Optimization Techniques (Debounce, Throttle, rAF, Web Workers)

### Debounce

Delay execution until activity stops.

```js
function debounce(fn, ms) {
  let t;
  return (...args) => {
    clearTimeout(t);
    t = setTimeout(() => fn(...args), ms);
  };
}
```

### Throttle

Limit calls to a max rate.

```js
function throttle(fn, ms) {
  let last = 0;
  return (...args) => {
    const now = Date.now();
    if (now - last >= ms) {
      last = now;
      fn(...args);
    }
  };
}
```

### requestAnimationFrame (rAF)

Use `rAF` for visual updates to sync with browser repaint.

```js
function animate() {
  // update DOM
  requestAnimationFrame(animate);
}
requestAnimationFrame(animate);
```

### Web Workers

Offload CPU-heavy tasks to background threads.

```js
// main.js
const worker = new Worker('worker.js');
worker.postMessage({ data });
worker.onmessage = e => console.log(e.data);
```

---

## 🔹 Rendering Optimizations (Reflows, Repaints, Layout)

* Minimize layout-triggering operations (reading layout properties like `offsetWidth` forces reflow).
* Batch DOM writes and reads separately (read → write pattern).
* Use `transform` and `opacity` for animations (GPU-accelerated) instead of `top/left`.

Example: cause of layout thrash

```js
for (let i=0;i<100;i++) {
  el.style.width = `${i}px`; // write
  console.log(el.offsetHeight); // read -> forces layout
}
```

Fix: collect reads first, then writes.

---

## 🔹 Network Performance (Caching, Compression, HTTP/2)

* Use HTTP caching headers (`Cache-Control`, `ETag`).
* Enable gzip or brotli compression.
* Use HTTP/2 multiplexing for fewer connections.
* Implement client-side caching and stale-while-revalidate patterns.

---

## 🔹 Build Optimizations (Tree Shaking, Code Splitting)

* Tree shaking removes unused exports (ESM only).
* Code splitting loads only required chunks (dynamic `import()`).
* Minify and compress bundles.

Example dynamic import for route-based splitting:

```js
// inside router
const Home = () => import('./Home.js');
```

---

## 🔹 Testing & Regression Detection (Lighthouse, Performance Budgets)

* Use **Lighthouse** to measure performance (FCP, LCP, TTFB, CLS).
* Set **performance budgets** to prevent regressions.
* Run performance tests in CI and compare metrics.

---

## 🔹 Practical Examples & Recipes

### 1. Catching and Annotating Errors

```js
try {
  risky();
} catch (err) {
  err.context = { userId: 123 };
  Sentry.captureException(err);
}
```

### 2. Graceful degradation for network failures

```js
try {
  const res = await fetch(url);
  if (!res.ok) throw new Error(`HTTP ${res.status}`);
  const data = await res.json();
} catch (err) {
  // show friendly UI and fallback
}
```

### 3. Profiling CPU hot path (DevTools)

* Open **Performance**, record, interact with app, stop → inspect heavy functions on flame chart → optimize.

---

## 🔹 Performance Optimization Flow Diagram

```
1. Observe symptoms (slow page, jank, high memory)
   │
2. Reproduce reliably or capture trace
   │
3. Profile (DevTools / Node profiler)
   │
4. Identify hot spots (CPU / Memory / Network)
   │
5. Apply fixes (debounce, code-split, web worker, caching)
   │
6. Re-profile and verify improvement
   │
7. Add tests/monitoring to catch regressions
```

---

## 🔹 Summary Table

| Topic                   | Key Action                           | Tooling                  |
| ----------------------- | ------------------------------------ | ------------------------ |
| Error Handling          | Use `try/catch`, custom errors       | Native JS                |
| Async Errors            | `try/await`, `.catch()`              | Node/Browser APIs        |
| Debugging               | Breakpoints, conditional breakpoints | Chrome DevTools, ndb     |
| Source Maps             | Map stacks to original source        | Webpack, Vite            |
| Logging                 | Structured logs, context             | Winston, Bunyan          |
| Monitoring              | Capture exceptions & performance     | Sentry, LogRocket        |
| CPU Profiling           | Flame charts, hot functions          | DevTools, clinic, 0x     |
| Memory Profiling        | Heap snapshots, allocation timeline  | DevTools, node-inspect   |
| Rendering Optimizations | Reduce reflows, use rAF              | DevTools Rendering panel |
| Network                 | Cache, compression, HTTP/2           | Browser Network panel    |

---

### ✅ In Short

* Handle errors explicitly and use monitoring to catch production issues.
* Use DevTools and Node profilers to find bottlenecks.
* Optimize rendering and networking alongside JS logic.
* Measure *before* and *after* changes, and automate performance checks in CI.

---

**Next Suggested Topic:** 🧩 *Advanced JavaScript Patterns — Functional Programming, Reactive Patterns, and Design Patterns (Complete Reference with Examples)*
