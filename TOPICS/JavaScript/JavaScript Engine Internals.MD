# Part VII — JavaScript Engine Internals

**V8, SpiderMonkey, and JavaScriptCore — Deep Dive into Compilation, Optimization, and Garbage Collection**

---

## Preface

Before diving into runtime environments, it’s crucial to understand the **engines** that make JavaScript possible. A JavaScript engine converts your high-level code into machine instructions the CPU can execute.

This section explores how engines like **V8**, **SpiderMonkey**, and **JavaScriptCore** parse, compile, and optimize your code at runtime. You’ll also learn how **Just-In-Time (JIT) compilation**, **hidden classes**, and **garbage collection** affect performance.

---

## Table of Contents

1. [Overview: What Is a JavaScript Engine?](#overview-what-is-a-javascript-engine)
2. [The Compilation Pipeline](#the-compilation-pipeline)
3. [Parsing & Abstract Syntax Tree (AST)](#parsing--abstract-syntax-tree-ast)
4. [Bytecode Generation & Interpretation](#bytecode-generation--interpretation)
5. [Just-In-Time (JIT) Compilation](#just-in-time-jit-compilation)
6. [Hidden Classes & Inline Caching](#hidden-classes--inline-caching)
7. [Deoptimization & Bailouts](#deoptimization--bailouts)
8. [Garbage Collection Internals](#garbage-collection-internals)
9. [Memory Model & Heap Layout](#memory-model--heap-layout)
10. [Comparing Major Engines](#comparing-major-engines)
11. [Performance Tips](#performance-tips)
12. [Summary](#summary)

---

## 1. Overview: What Is a JavaScript Engine?

A **JavaScript engine** reads, parses, compiles, and executes your JavaScript code. It bridges human-readable JavaScript and low-level machine code.

### Popular Engines

| Engine             | Developer | Used In               |
| ------------------ | --------- | --------------------- |
| **V8**             | Google    | Chrome, Node.js, Deno |
| **SpiderMonkey**   | Mozilla   | Firefox               |
| **JavaScriptCore** | Apple     | Safari                |

Each engine performs the same fundamental steps:

* Parse the code → Build **AST**
* Generate **bytecode or machine code**
* Optimize via **JIT compilation**
* Manage memory with **Garbage Collection (GC)**

---

## 2. The Compilation Pipeline

```
Source Code
   ↓
Parser → AST → Bytecode → Machine Code
   ↑         ↓
  Scope   Interpreter
            ↓
        JIT Compiler
```

1. **Parser** — Converts source code into an Abstract Syntax Tree (AST).
2. **Interpreter** — Executes the code line-by-line or from bytecode.
3. **Profiler** — Detects frequently executed ("hot") code.
4. **JIT Compiler** — Optimizes hot code into machine code.
5. **Deoptimizer** — Reverts optimizations if assumptions fail.

---

## 3. Parsing & Abstract Syntax Tree (AST)

Parsing transforms JavaScript code into a structured **AST**.

### Example

```js
const sum = (a, b) => a + b;
```

Simplified AST structure:

```
Program
 └── VariableDeclaration (sum)
      └── ArrowFunctionExpression
           ├── Identifier(a)
           ├── Identifier(b)
           └── BinaryExpression(+)
```

Use [astexplorer.net](https://astexplorer.net) to visualize ASTs.

---

## 4. Bytecode Generation & Interpretation

The AST is transformed into **bytecode**, an intermediate, low-level representation.

### Example

```js
let x = 10;
let y = 20;
let z = x + y;
```

Simplified pseudo-bytecode:

```
LdaSmi 10
StaGlobal x
LdaSmi 20
StaGlobal y
LdaGlobal x
Add y
StaGlobal z
```

### Interpreter (e.g., Ignition in V8)

* Executes bytecode instruction-by-instruction.
* Collects profiling data for the JIT compiler.

---

## 5. Just-In-Time (JIT) Compilation

**JIT** compilation optimizes performance by compiling frequently run (“hot”) code into machine code.

### In V8:

* **Ignition** → Interpreter (baseline execution).
* **TurboFan** → JIT compiler (optimized execution).
* **Deoptimizer** → Rolls back if types change or assumptions fail.

Flow:

```
Parse → Interpret → Optimize → Execute → Deoptimize (if needed)
```

This hybrid model gives JavaScript both **fast startup** and **optimized runtime speed**.

---

## 6. Hidden Classes & Inline Caching

### Hidden Classes

V8 assigns **hidden classes** (internal blueprints) to objects to optimize property access.

```js
const user = { name: 'Alice', age: 30 };
user.email = 'a@example.com'; // changes hidden class
```

* Adding/removing properties dynamically can cause **deoptimization**.
* Keep consistent object shapes for performance.

### Inline Caching

```js
function greet(user) {
  return user.name;
}
```

After the first execution, V8 caches the property lookup, so repeated calls are faster.

---

## 7. Deoptimization & Bailouts

JIT compilers make assumptions about types and shapes. When these assumptions break, **deoptimization** occurs.

```js
function add(a, b) {
  return a + b;
}

add(2, 3);     // optimized (number)
add('a', 'b'); // deoptimized (string)
```

Type changes cause a fallback to interpreted bytecode.

---

## 8. Garbage Collection Internals

Engines like V8 use **Generational Garbage Collection** to reclaim memory.

| Generation    | Description         | Example                |
| ------------- | ------------------- | ---------------------- |
| **New Space** | Short-lived objects | Function scope vars    |
| **Old Space** | Long-lived objects  | Global or closure vars |

### GC Phases

1. **Mark** — Mark reachable objects.
2. **Sweep** — Free unmarked memory.
3. **Compact** — Rearrange memory for efficiency.

Optimizations include **incremental**, **parallel**, and **concurrent** GC cycles.

---

## 9. Memory Model & Heap Layout

```
┌─────────────────────────────┐
│ Stack (call frames)         │
├─────────────────────────────┤
│ Heap (objects, closures)    │
├─────────────────────────────┤
│ Code Space (compiled code)  │
└─────────────────────────────┘
```

* **Stack** — Local variables, function calls.
* **Heap** — Dynamic objects and arrays.
* **Code Space** — Machine code and JIT cache.

---

## 10. Comparing Major Engines

| Feature      | V8           | SpiderMonkey         | JavaScriptCore |
| ------------ | ------------ | -------------------- | -------------- |
| Interpreter  | Ignition     | Baseline Interpreter | LLInt          |
| JIT Compiler | TurboFan     | IonMonkey            | DFG / FTL      |
| GC Type      | Generational | Incremental          | Concurrent     |
| Written In   | C++          | C++                  | C++            |
| Used In      | Chrome, Node | Firefox              | Safari         |

---

## 11. Performance Tips

✅ Keep object shapes consistent.
✅ Avoid adding/removing properties dynamically.
✅ Use consistent data types.
✅ Avoid global variables.
✅ Don’t nest functions unnecessarily.
✅ Use `for...of` or array methods instead of `for...in`.

---

## 12. Summary

| Concept            | Description                                   |
| ------------------ | --------------------------------------------- |
| **AST**            | Tree representation of source code            |
| **Bytecode**       | Intermediate code executed by the interpreter |
| **JIT Compiler**   | Optimizes hot code into machine code          |
| **Hidden Classes** | Optimize object shape consistency             |
| **Inline Caching** | Cache property lookups for speed              |
| **Deoptimization** | Fallback when type assumptions fail           |
| **GC**             | Automatically frees unused memory             |

---

**Next Section:**
⚙️ *Part VIII — JavaScript Runtime Environments & Memory Management (Browser vs Node.js)*
