# 🧪 TypeScript Mastery Series – Part IX: Testing with TypeScript

---

## 📘 Table of Contents

1. [Introduction](#introduction)
2. [Setting Up Jest with TypeScript](#setting-up-jest-with-typescript)
3. [Configuring ts-jest](#configuring-ts-jest)
4. [Writing Unit Tests](#writing-unit-tests)
5. [Mocking and Spying](#mocking-and-spying)
6. [Testing Async Code](#testing-async-code)
7. [Integration Testing](#integration-testing)
8. [End-to-End (E2E) Testing Setup](#end-to-end-e2e-testing-setup)
9. [Code Coverage](#code-coverage)
10. [Testing Best Practices](#testing-best-practices)
11. [Summary](#summary)

---

## 🧠 Introduction

Testing ensures your TypeScript applications work as intended and remain stable as they evolve. TypeScript’s static typing helps catch many errors early, but **unit, integration, and end-to-end testing** are still vital for ensuring reliability.

We’ll use **Jest**, one of the most popular testing frameworks for TypeScript projects.

---

## ⚙️ Setting Up Jest with TypeScript

### Step 1: Install Dependencies

```bash
npm install --save-dev jest ts-jest @types/jest typescript
```

### Step 2: Initialize Jest Config

```bash
npx ts-jest config:init
```

This creates a `jest.config.ts` file.

### Example `jest.config.ts`

```ts
import type { Config } from 'jest';

const config: Config = {
  preset: 'ts-jest',
  testEnvironment: 'node',
  roots: ['<rootDir>/src'],
  moduleFileExtensions: ['ts', 'js'],
  transform: {
    '^.+\\.ts$': 'ts-jest'
  }
};

export default config;
```

### Step 3: Add a Test Script

```json
"scripts": {
  "test": "jest --passWithNoTests"
}
```

---

## 🧩 Configuring ts-jest

`ts-jest` compiles TypeScript files directly in Jest, without requiring a separate build step.

### Example Jest + TypeScript Integration

```bash
npm install --save-dev ts-jest
```

Jest will automatically recognize `.ts` files through `ts-jest`.

For advanced configuration, you can add `globals` in `jest.config.ts`:

```ts
globals: {
  'ts-jest': {
    tsconfig: 'tsconfig.json'
  }
}
```

---

## 🧾 Writing Unit Tests

Create a file `src/utils/math.ts`:

```ts
export function add(a: number, b: number): number {
  return a + b;
}

export function multiply(a: number, b: number): number {
  return a * b;
}
```

Now add a test: `src/utils/math.test.ts`

```ts
import { add, multiply } from './math';

test('adds two numbers', () => {
  expect(add(2, 3)).toBe(5);
});

test('multiplies two numbers', () => {
  expect(multiply(2, 3)).toBe(6);
});
```

Run tests:

```bash
npm test
```

Output:

```
PASS src/utils/math.test.ts
✓ adds two numbers (3 ms)
✓ multiplies two numbers (2 ms)
```

---

## 🕵️ Mocking and Spying

Mocking replaces real implementations with fake ones for isolated testing.

### Example: Manual Mock

```ts
// emailService.ts
export function sendEmail(to: string, message: string) {
  return `Email sent to ${to}`;
}

// emailService.test.ts
import * as emailService from './emailService';

jest.spyOn(emailService, 'sendEmail').mockReturnValue('Mocked Email');

test('mocks sendEmail', () => {
  const result = emailService.sendEmail('test@example.com', 'Hello');
  expect(result).toBe('Mocked Email');
});
```

---

## ⏳ Testing Async Code

Use `async/await` for asynchronous tests.

```ts
const fetchData = async () => {
  return new Promise((resolve) => setTimeout(() => resolve('Data received'), 100));
};

test('fetches data asynchronously', async () => {
  const data = await fetchData();
  expect(data).toBe('Data received');
});
```

You can also use `.resolves` and `.rejects`:

```ts
await expect(fetchData()).resolves.toBe('Data received');
```

---

## 🧩 Integration Testing

Integration tests check how modules interact together.

Example – Express API:

```ts
import express from 'express';
import request from 'supertest';

const app = express();
app.get('/hello', (req, res) => res.json({ message: 'Hello World' }));

test('GET /hello returns message', async () => {
  const res = await request(app).get('/hello');
  expect(res.status).toBe(200);
  expect(res.body.message).toBe('Hello World');
});
```

Install `supertest` for HTTP testing:

```bash
npm install --save-dev supertest @types/supertest
```

---

## 🧱 End-to-End (E2E) Testing Setup

For E2E testing, use frameworks like **Playwright** or **Cypress**.

### Example (Playwright)

```bash
npm install -D @playwright/test
npx playwright install
```

Create `tests/example.spec.ts`:

```ts
import { test, expect } from '@playwright/test';

test('homepage has title', async ({ page }) => {
  await page.goto('https://example.com');
  await expect(page).toHaveTitle(/Example Domain/);
});
```

Run tests:

```bash
npx playwright test
```

---

## 📊 Code Coverage

Jest can generate coverage reports automatically.

Run:

```bash
npm test -- --coverage
```

It generates a `/coverage` folder with HTML and text reports.

### Example Output

```
File              | % Stmts | % Branch | % Funcs | % Lines |
------------------|---------|----------|---------|---------|
src/utils/math.ts |   100%  |   100%   |  100%   |  100%  |
```

---

## ✅ Testing Best Practices

* Keep test files near implementation files (`component.test.tsx`).
* Use descriptive test names.
* Mock external dependencies (DB, APIs).
* Ensure unit tests run fast (<500ms).
* Use **integration tests** for service-level logic.
* Use **E2E tests** for full user flow validation.
* Include coverage reports in CI/CD pipelines.

---

## 🧾 Summary

* Use **Jest + ts-jest** for smooth TypeScript testing.
* Write **unit, integration, and E2E** tests for complete coverage.
* Use **async/await** for async testing.
* Generate coverage and integrate it into CI pipelines.
* Follow best practices for scalable, maintainable test suites.

---

Next: [Part X – Advanced Practices & Design Patterns →](#)
