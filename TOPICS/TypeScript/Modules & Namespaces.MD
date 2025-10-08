# 🧱 TypeScript Mastery Series – Part V: Modules & Namespaces

---

## 📘 Table of Contents

1. [Introduction](#introduction)
2. [Understanding Modules](#understanding-modules)
3. [ES Modules vs CommonJS](#es-modules-vs-commonjs)
4. [Import and Export Syntax](#import-and-export-syntax)
5. [Default vs Named Exports](#default-vs-named-exports)
6. [Re-exporting Modules](#re-exporting-modules)
7. [Type-only Imports and Exports](#type-only-imports-and-exports)
8. [Dynamic Imports](#dynamic-imports)
9. [Namespaces (Legacy Feature)](#namespaces-legacy-feature)
10. [Declaration Merging](#declaration-merging)
11. [Summary](#summary)

---

## 🧠 Introduction

Modules and namespaces are how TypeScript organizes and scopes code. Modern TypeScript primarily uses **ES modules**, while **namespaces** remain for legacy or internal modularization.

---

## 📦 Understanding Modules

A **module** in TypeScript is any file containing `import` or `export` statements.
Each module has its own scope, meaning variables and functions inside are **not global**.

```ts
// math.ts
export function add(a: number, b: number): number {
  return a + b;
}

// main.ts
import { add } from './math';
console.log(add(5, 3));
```

---

## ⚙️ ES Modules vs CommonJS

TypeScript supports both **ES Modules (ESM)** and **CommonJS** systems.

| Feature    | ES Modules            | CommonJS                       |
| ---------- | --------------------- | ------------------------------ |
| Syntax     | `import` / `export`   | `require()` / `module.exports` |
| Default in | Frontend / ESM builds | Node.js (legacy)               |
| Config key | `"module": "ESNext"`  | `"module": "commonjs"`         |

Example (CommonJS):

```ts
// math.js
exports.add = (a, b) => a + b;

// main.js
const { add } = require('./math');
```

---

## 📤 Import and Export Syntax

### Named Exports

```ts
export const PI = 3.14;
export function multiply(a: number, b: number): number {
  return a * b;
}
```

### Importing Named Exports

```ts
import { PI, multiply } from './math';
console.log(multiply(PI, 2));
```

### Aliasing

```ts
import { multiply as times } from './math';
times(2, 3);
```

---

## 📦 Default vs Named Exports

### Default Export (one per file)

```ts
// user.ts
export default class User {
  constructor(public name: string) {}
}

// main.ts
import User from './user';
const user = new User('Akash');
```

### Mixed Exports

```ts
export default function greet() {
  console.log('Hello');
}
export const version = '1.0.0';
```

---

## 🔁 Re-exporting Modules

Re-exporting allows consolidating exports from multiple files.

```ts
// utils/math.ts
export const add = (a: number, b: number) => a + b;
export const sub = (a: number, b: number) => a - b;

// utils/index.ts
export * from './math';

// main.ts
import { add } from './utils';
```

You can also rename during re-export:

```ts
export { add as addition } from './math';
```

---

## 🧩 Type-only Imports and Exports

TypeScript allows importing or exporting **types only**, which get erased at compile time.

```ts
// types.ts
export type User = { id: number; name: string };

// app.ts
import type { User } from './types';
const newUser: User = { id: 1, name: 'Akash' };
```

Exporting only types:

```ts
export type { User } from './types';
```

---

## ⚡ Dynamic Imports

Dynamic imports load modules lazily.

```ts
async function loadMath() {
  const math = await import('./math.js');
  console.log(math.add(5, 10));
}

loadMath();
```

Useful for performance optimization and lazy-loading features.

---

## 🧱 Namespaces (Legacy Feature)

Before ES Modules, TypeScript used **namespaces** to group related code.

```ts
namespace Geometry {
  export function areaCircle(radius: number): number {
    return Math.PI * radius ** 2;
  }

  export function areaSquare(side: number): number {
    return side * side;
  }
}

console.log(Geometry.areaCircle(10));
```

Namespaces are now mostly replaced by ES Modules but are still useful for **internal modularization** in large projects.

---

## 🧩 Declaration Merging

TypeScript allows **merging multiple declarations** with the same name — applicable for **interfaces**, **namespaces**, and **functions**.

```ts
interface User {
  name: string;
}

interface User {
  age: number;
}

const u: User = { name: 'Akash', age: 30 };
```

Namespace + Function merge example:

```ts
function greet(name: string) {
  return `Hello ${name}`;
}

namespace greet {
  export const version = '1.0.0';
}

console.log(greet('Akash'));
console.log(greet.version);
```

---

## 🧾 Summary

* **Modules** organize code into reusable files with their own scope.
* **ES Modules** are now standard; **CommonJS** is mainly for legacy Node.js code.
* Use **named** and **default** exports strategically.
* **Type-only imports/exports** improve type safety without runtime cost.
* **Namespaces** are legacy but useful for grouping related logic.
* **Declaration merging** enables flexible augmentation of existing structures.

---

Next: [Part VI – TypeScript with Node.js →](#)
