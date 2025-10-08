# 🟢 TypeScript Mastery Series – Part VI: TypeScript with Node.js

---

## 📘 Table of Contents

1. [Introduction](#introduction)
2. [Setting Up TypeScript for Node.js](#setting-up-typescript-for-nodejs)
3. [Understanding Type Definitions](#understanding-type-definitions)
4. [Using tsconfig.json for Node Projects](#using-tsconfigjson-for-node-projects)
5. [Building a Simple Express Server](#building-a-simple-express-server)
6. [Async/Await and Promises with Types](#asyncawait-and-promises-with-types)
7. [Error Handling in Node + TypeScript](#error-handling-in-node--typescript)
8. [Environment Variables and Configuration](#environment-variables-and-configuration)
9. [Module Resolution and Path Aliases](#module-resolution-and-path-aliases)
10. [Best Practices for Node + TypeScript](#best-practices-for-node--typescript)
11. [Summary](#summary)

---

## 🧠 Introduction

TypeScript enhances Node.js development by adding **type safety**, **tooling support**, and **better maintainability**. It ensures that server-side code is predictable and free from runtime type errors.

In this part, you’ll learn how to set up TypeScript for Node.js, use it with Express, and follow best practices for production-grade TypeScript backends.

---

## ⚙️ Setting Up TypeScript for Node.js

### Step 1: Initialize the Project

```bash
mkdir ts-node-app && cd ts-node-app
npm init -y
```

### Step 2: Install TypeScript and Node Types

```bash
npm install typescript ts-node @types/node --save-dev
```

### Step 3: Initialize TypeScript Config

```bash
npx tsc --init
```

### Example Directory Structure

```
ts-node-app/
├── src/
│   └── index.ts
├── dist/
└── tsconfig.json
```

### Example `tsconfig.json`

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "CommonJS",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true
  }
}
```

---

## 🧾 Understanding Type Definitions

TypeScript uses **declaration files** (`.d.ts`) to describe the types of JavaScript libraries.

Example: installing Node.js types

```bash
npm install --save-dev @types/node
```

### Example usage

```ts
import fs from 'fs';

const content = fs.readFileSync('package.json', 'utf-8');
console.log(content);
```

The `@types/node` package adds type definitions for built-in Node modules like `fs`, `http`, and `path`.

---

## 🧰 Using tsconfig.json for Node Projects

The `tsconfig.json` file tells the TypeScript compiler how to process your code.

Common Node-specific options:

| Option            | Description                            |
| ----------------- | -------------------------------------- |
| `target`          | Specifies the JS version output        |
| `module`          | CommonJS for Node apps                 |
| `outDir`          | Folder for compiled output             |
| `rootDir`         | Root folder for TypeScript source code |
| `esModuleInterop` | Enables default import compatibility   |

---

## 🚀 Building a Simple Express Server

### Step 1: Install Express and Types

```bash
npm install express
npm install --save-dev @types/express
```

### Step 2: Create `src/server.ts`

```ts
import express, { Request, Response } from 'express';

const app = express();
const PORT = 3000;

app.get('/', (req: Request, res: Response) => {
  res.send('Hello TypeScript + Node!');
});

app.listen(PORT, () => console.log(`Server running at http://localhost:${PORT}`));
```

### Step 3: Run the Server

```bash
npx ts-node src/server.ts
```

---

## ⏳ Async/Await and Promises with Types

TypeScript allows strict typing for async operations.

```ts
async function fetchData(url: string): Promise<string> {
  const response = await fetch(url);
  return response.text();
}

fetchData('https://example.com').then(console.log);
```

If a function doesn’t return a value:

```ts
async function logMessage(message: string): Promise<void> {
  console.log(message);
}
```

---

## ⚠️ Error Handling in Node + TypeScript

Error handling is crucial in production.

### Example – Try/Catch with async

```ts
async function getUserData(id: number) {
  try {
    if (!id) throw new Error('Invalid ID');
    return { id, name: 'Akash' };
  } catch (error) {
    console.error('Error:', (error as Error).message);
  }
}
```

### Custom Error Classes

```ts
class AppError extends Error {
  constructor(public code: number, message: string) {
    super(message);
  }
}

throw new AppError(404, 'User not found');
```

---

## 🔐 Environment Variables and Configuration

Use the `dotenv` package for managing environment variables.

```bash
npm install dotenv
```

```ts
import dotenv from 'dotenv';
dotenv.config();

console.log(process.env.DB_HOST);
```

Add a `.env` file:

```
DB_HOST=localhost
DB_USER=admin
DB_PASS=secret
```

And include it in `.gitignore`.

---

## 🧭 Module Resolution and Path Aliases

You can simplify imports using **path aliases**.

In `tsconfig.json`:

```json
{
  "compilerOptions": {
    "baseUrl": "./src",
    "paths": {
      "@controllers/*": ["controllers/*"],
      "@models/*": ["models/*"]
    }
  }
}
```

Now import with:

```ts
import UserController from '@controllers/UserController';
```

---

## 💡 Best Practices for Node + TypeScript

✅ Use `strict` mode in `tsconfig.json` for safety.
✅ Organize your project using `src/` for TypeScript and `dist/` for compiled files.
✅ Keep type definitions (`.d.ts`) separate if writing custom types.
✅ Always handle promise rejections with `try/catch`.
✅ Use `@types` for all third-party libraries.
✅ Enable source maps for easier debugging:

```json
"sourceMap": true
```

---

## 🧾 Summary

* TypeScript works seamlessly with Node.js through `ts-node` and `@types/node`.
* Express + TypeScript enables type-safe backend APIs.
* Use path aliases and config-driven builds for scalable architecture.
* Handle async operations and errors properly.
* Manage environment variables securely with `dotenv`.

---

Next: [Part VII – TypeScript with React →](#)
