# Part IX — Testing & Tooling

**Unit, Integration, and E2E Testing — Linting, Debugging, and Developer Tooling in JavaScript**

---

## Preface

Testing and tooling form the backbone of robust JavaScript development. Well-tested code ensures maintainability, reliability, and confidence in deployments. This section covers different **testing strategies**, **frameworks**, and **tooling** — including **linting**, **formatting**, and **debugging** — essential for professional-level JavaScript projects.

---

## Table of Contents

1. [Testing Overview](#testing-overview)
2. [Types of Testing](#types-of-testing)
3. [Unit Testing](#unit-testing)
4. [Integration Testing](#integration-testing)
5. [End-to-End (E2E) Testing](#end-to-end-e2e-testing)
6. [Mocking, Spies, and Stubs](#mocking-spies-and-stubs)
7. [Property-Based and Mutation Testing](#property-based-and-mutation-testing)
8. [Testing Frameworks and Tools](#testing-frameworks-and-tools)
9. [Linting and Formatting](#linting-and-formatting)
10. [Debugging and Profiling](#debugging-and-profiling)
11. [Static Analysis and Security Tools](#static-analysis-and-security-tools)
12. [Summary](#summary)

---

## 1. Testing Overview

Testing ensures code correctness, performance, and regression safety. In JavaScript, it’s especially critical due to the language’s **dynamic nature** and **loose typing**.

| Type            | Scope                | Purpose                         |
| --------------- | -------------------- | ------------------------------- |
| **Unit**        | Individual functions | Validate logic correctness      |
| **Integration** | Modules combined     | Ensure module interaction works |
| **E2E**         | Full system          | Simulate real-world user flow   |

---

## 2. Types of Testing

| Test Type             | Example Tools       | Use Case                           |
| --------------------- | ------------------- | ---------------------------------- |
| **Unit Tests**        | Jest, Mocha         | Small isolated components          |
| **Integration Tests** | Jest, Supertest     | Verify API routes, DB interactions |
| **E2E Tests**         | Cypress, Playwright | UI flow and user journey           |
| **Snapshot Tests**    | Jest                | UI consistency validation          |
| **Mutation Tests**    | Stryker             | Detect weak tests                  |

---

## 3. Unit Testing

Unit tests validate **single functions or modules** in isolation.

### Example (Using Jest)

```js
function add(a, b) {
  return a + b;
}

module.exports = add;
```

```js
// add.test.js
const add = require('./add');

test('adds 2 + 3 = 5', () => {
  expect(add(2, 3)).toBe(5);
});
```

Run with:

```bash
npx jest
```

✅ Fast feedback loop
✅ High isolation
⚠️ Limited scope (no system integration)

---

## 4. Integration Testing

Integration tests ensure that **modules or components work together correctly**.

### Example (Node.js API)

```js
const request = require('supertest');
const app = require('../app');

describe('GET /users', () => {
  it('should return list of users', async () => {
    const res = await request(app).get('/users');
    expect(res.statusCode).toBe(200);
    expect(res.body).toHaveProperty('users');
  });
});
```

---

## 5. End-to-End (E2E) Testing

E2E tests validate the **entire flow** — from frontend interaction to backend response.

### Example (Cypress)

```js
describe('Login Flow', () => {
  it('should login successfully', () => {
    cy.visit('/login');
    cy.get('#email').type('test@example.com');
    cy.get('#password').type('123456');
    cy.get('button[type="submit"]').click();
    cy.url().should('include', '/dashboard');
  });
});
```

✅ Tests complete workflow
⚠️ Slower and requires setup (browser, DB, etc.)

---

## 6. Mocking, Spies, and Stubs

### Mocking

Replace dependencies with **mocked behavior**.

```js
jest.mock('./api');
const api = require('./api');
api.getData.mockResolvedValue({ name: 'Akash' });
```

### Spies

Track function calls.

```js
const spy = jest.spyOn(console, 'log');
console.log('hello');
expect(spy).toHaveBeenCalledWith('hello');
```

### Stubs

Replace functions temporarily.

```js
sinon.stub(Math, 'random').returns(0.42);
```

---

## 7. Property-Based and Mutation Testing

**Property-based testing** validates rules rather than examples.

```js
const fc = require('fast-check');
const add = (a, b) => a + b;

fc.assert(fc.property(fc.integer(), fc.integer(), (a, b) => {
  return add(a, b) === add(b, a);
}));
```

**Mutation testing** introduces changes (“mutants”) to ensure tests catch them.

Tool: [Stryker Mutator](https://stryker-mutator.io)

---

## 8. Testing Frameworks and Tools

| Framework                | Type              | Highlights                  |
| ------------------------ | ----------------- | --------------------------- |
| **Jest**                 | Unit, Integration | Simple API, built-in mocks  |
| **Mocha + Chai + Sinon** | Unit              | Flexible, composable tools  |
| **Cypress**              | E2E               | Real browser automation     |
| **Playwright**           | E2E               | Cross-browser testing       |
| **Vitest**               | Unit              | Vite-native, fast startup   |
| **Testing Library**      | UI                | React, Vue, Angular support |

---

## 9. Linting and Formatting

**Linting** helps enforce coding standards and detect errors.
**Formatting** ensures consistent style.

### ESLint Example

```bash
npm install eslint --save-dev
npx eslint --init
```

Create `.eslintrc.json`:

```json
{
  "env": { "browser": true, "es2021": true },
  "extends": ["eslint:recommended", "prettier"],
  "rules": { "no-console": "warn" }
}
```

### Prettier Example

```bash
npm install prettier --save-dev
npx prettier --write .
```

---

## 10. Debugging and Profiling

### Browser Debugging

* Chrome DevTools → Sources tab → Breakpoints.
* Use `debugger` keyword in code.

### Node.js Debugging

```bash
node --inspect app.js
chrome://inspect
```

### Profiling Performance

* Chrome → Performance tab → Flame chart.
* Node → `--prof` flag for CPU profiling.

---

## 11. Static Analysis and Security Tools

| Tool           | Purpose                          |
| -------------- | -------------------------------- |
| **TypeScript** | Static type analysis             |
| **SonarQube**  | Code quality and maintainability |
| **ESLint**     | Code correctness                 |
| **npm audit**  | Vulnerability detection          |
| **Snyk**       | Dependency security              |
| **Prettier**   | Consistent formatting            |

---

## 12. Summary

| Concept                 | Description                              |
| ----------------------- | ---------------------------------------- |
| **Unit Testing**        | Small, isolated function validation      |
| **Integration Testing** | Ensures module communication correctness |
| **E2E Testing**         | Full system simulation                   |
| **Mocking/Spies**       | Replace or monitor dependencies          |
| **Linting**             | Static error detection                   |
| **Formatting**          | Consistent code style                    |
| **Debugging/Profiling** | Diagnose and optimize performance        |

---

**Next Section:**
🧭 *Part X — Architecture & System Patterns (Microservices, Frontend Architecture, State Management, and Security)*
