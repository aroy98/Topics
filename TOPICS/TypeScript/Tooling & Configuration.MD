# 🧰 TypeScript Mastery Series – Part VIII: Tooling & Configuration

---

## 📘 Table of Contents

1. [Introduction](#introduction)
2. [Essential Development Tools](#essential-development-tools)
3. [Configuring tsconfig.json](#configuring-tsconfigjson)
4. [Linting and Formatting with ESLint + Prettier](#linting-and-formatting-with-eslint--prettier)
5. [Path Aliases and Module Resolution](#path-aliases-and-module-resolution)
6. [Type Checking and Build Optimization](#type-checking-and-build-optimization)
7. [Integrating TypeScript with Babel](#integrating-typescript-with-babel)
8. [Debugging TypeScript Code](#debugging-typescript-code)
9. [Build Tools (Vite, Webpack, tsup, esbuild)](#build-tools-vite-webpack-tsup-esbuild)
10. [Improving DX with VSCode](#improving-dx-with-vscode)
11. [Summary](#summary)

---

## 🧠 Introduction

Tooling is the backbone of any TypeScript project. Proper configuration ensures **faster builds**, **error-free code**, and **a smooth developer experience (DX)**. This section walks through essential tools, configuration options, and optimizations for scalable TypeScript projects.

---

## 🧩 Essential Development Tools

| Tool                            | Purpose                                   |
| ------------------------------- | ----------------------------------------- |
| **TypeScript Compiler (`tsc`)** | Transpiles `.ts` → `.js`                  |
| **ESLint**                      | Linting and code quality enforcement      |
| **Prettier**                    | Code formatting consistency               |
| **ts-node**                     | Run TypeScript directly without compiling |
| **nodemon**                     | Auto-restart on code changes              |
| **Vite / Webpack / tsup**       | Bundling and building                     |
| **VSCode**                      | Rich IDE with native TypeScript support   |

Example setup:

```bash
npm install -D typescript ts-node nodemon eslint prettier
```

---

## ⚙️ Configuring tsconfig.json

The `tsconfig.json` file controls how TypeScript compiles your project.

### Example (recommended base config)

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "strict": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "skipLibCheck": true,
    "outDir": "./dist",
    "rootDir": "./src",
    "baseUrl": "./src",
    "paths": {
      "@components/*": ["components/*"],
      "@utils/*": ["utils/*"]
    }
  },
  "include": ["src"],
  "exclude": ["node_modules", "dist"]
}
```

### Common Options Explained

| Option            | Description                                  |
| ----------------- | -------------------------------------------- |
| `strict`          | Enables all type safety checks               |
| `noImplicitAny`   | Prevents variables without types             |
| `esModuleInterop` | Allows default import from CommonJS modules  |
| `skipLibCheck`    | Skips checking `.d.ts` files for performance |
| `sourceMap`       | Generates `.map` files for debugging         |

---

## 🧹 Linting and Formatting with ESLint + Prettier

### Step 1: Install Dependencies

```bash
npm install -D eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin prettier eslint-config-prettier eslint-plugin-prettier
```

### Step 2: Configure ESLint

Create `.eslintrc.json`:

```json
{
  "parser": "@typescript-eslint/parser",
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:prettier/recommended"
  ],
  "plugins": ["@typescript-eslint"],
  "env": { "browser": true, "node": true, "es2022": true }
}
```

### Step 3: Add Prettier Config

Create `.prettierrc`:

```json
{
  "singleQuote": true,
  "semi": true,
  "trailingComma": "es5"
}
```

Run linting:

```bash
npx eslint src --ext .ts,.tsx
```

---

## 📦 Path Aliases and Module Resolution

Path aliases make imports cleaner and reduce relative path complexity.

```json
"baseUrl": "./src",
"paths": {
  "@models/*": ["models/*"],
  "@services/*": ["services/*"]
}
```

Usage:

```ts
import { User } from '@models/User';
```

In Node, use **tsconfig-paths**:

```bash
npm install tsconfig-paths
```

And run:

```bash
node -r tsconfig-paths/register dist/index.js
```

---

## 🚀 Type Checking and Build Optimization

### Incremental Compilation

```json
"incremental": true,
"tsBuildInfoFile": "./.tsbuildinfo"
```

### Composite Projects (for monorepos)

```json
"composite": true,
"declaration": true,
"declarationMap": true
```

### Skipping Library Checks

To speed up compilation:

```json
"skipLibCheck": true
```

---

## 🔄 Integrating TypeScript with Babel

For front-end projects (React, Next.js, etc.), use **Babel** to transpile TypeScript faster.

Install dependencies:

```bash
npm install -D @babel/core @babel/preset-env @babel/preset-typescript
```

Create `.babelrc`:

```json
{
  "presets": ["@babel/preset-env", "@babel/preset-typescript"]
}
```

Then build:

```bash
npx babel src --out-dir dist --extensions ".ts,.tsx"
```

---

## 🧩 Debugging TypeScript Code

Enable `sourceMap` in `tsconfig.json`:

```json
"sourceMap": true
```

In VSCode, add `.vscode/launch.json`:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Debug TS App",
      "skipFiles": ["<node_internals>/**"],
      "program": "${workspaceFolder}/src/index.ts",
      "preLaunchTask": "tsc: build - tsconfig.json",
      "outFiles": ["${workspaceFolder}/dist/**/*.js"]
    }
  ]
}
```

---

## 🏗️ Build Tools (Vite, Webpack, tsup, esbuild)

| Tool        | Description                 | Best For                |
| ----------- | --------------------------- | ----------------------- |
| **Vite**    | Lightning-fast dev server   | Front-end apps          |
| **Webpack** | Highly configurable bundler | Enterprise apps         |
| **tsup**    | Minimal TS bundler          | Libraries & CLI tools   |
| **esbuild** | Ultra-fast bundler          | Prototypes / dev builds |

Example with **tsup**:

```bash
npm install -D tsup
```

```json
{
  "scripts": {
    "build": "tsup src/index.ts --format esm,cjs --dts"
  }
}
```

---

## 💡 Improving DX with VSCode

### Recommended VSCode Extensions

* **ESLint** – Inline lint feedback
* **Prettier** – Auto-formatting
* **TypeScript Hero** – Import management
* **Path Intellisense** – Auto-completion for paths

### Enable Auto-Fix on Save

In `.vscode/settings.json`:

```json
{
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }
}
```

---

## 🧾 Summary

* `tsconfig.json` defines TypeScript build behavior.
* Use **ESLint + Prettier** for consistent code quality.
* Optimize builds with incremental and composite compilation.
* Use **path aliases** to simplify imports.
* Integrate TypeScript with **Babel**, **Vite**, or **Webpack** for production-ready pipelines.
* Debug efficiently using VSCode with source maps.

---

Next: [Part IX – Testing with TypeScript →](#)
