# 🧪 React Mastery Series – Part VII: Testing & CI/CD

---

## 📘 Table of Contents

1. [Introduction](#introduction)
2. [Testing Pyramid & Strategy](#testing-pyramid--strategy)
3. [Unit & Component Testing with Jest + React Testing Library](#unit--component-testing-with-jest--react-testing-library)

   * [Setup](#setup)
   * [Writing Tests](#writing-tests)
   * [Testing Hooks](#testing-hooks)
   * [Mocking & Stubbing](#mocking--stubbing)
4. [Integration Testing Patterns](#integration-testing-patterns)
5. [End-to-End Testing with Cypress](#end-to-end-testing-with-cypress)

   * [Setup](#setup-1)
   * [Writing E2E Tests](#writing-e2e-tests)
   * [Network Stubbing & Fixtures](#network-stubbing--fixtures)
6. [Visual Regression Testing (Storybook + Chromatic)](#visual-regression-testing-storybook--chromatic)
7. [Test Data & Test Doubles](#test-data--test-doubles)
8. [Accessibility Testing (a11y)](#accessibility-testing-a11y)
9. [Performance & Load Testing](#performance--load-testing)
10. [CI/CD Integration (GitHub Actions Example)](#cicd-integration-github-actions-example)

    * [Caching & Parallelization](#caching--parallelization)
    * [Publishing Artifacts & Reports](#publishing-artifacts--reports)
11. [Test Flakiness & Reliability](#test-flakiness--reliability)
12. [Best Practices & Checklist](#best-practices--checklist)
13. [Summary](#summary)

---

## 🧠 Introduction

This part covers a full testing and CI/CD strategy for React applications: unit/component testing with **Jest + React Testing Library**, end-to-end tests with **Cypress**, visual regression with **Storybook + Chromatic**, accessibility checks, performance testing, and a practical **GitHub Actions** CI pipeline that runs tests, lints, and publishes reports.

---

## 🧩 Testing Pyramid & Strategy

* **Unit tests** (fast) — small functions and pure logic.
* **Component tests** (medium) — components rendered in isolation (React Testing Library).
* **Integration tests** (slower) — multiple components + modules working together.
* **End-to-end tests** (slowest) — full user flows (Cypress).

Aim for more unit/component tests and fewer slow E2E tests.

---

## 🧪 Unit & Component Testing with Jest + React Testing Library

### Setup

```bash
npm install -D jest @types/jest ts-jest @testing-library/react @testing-library/jest-dom @testing-library/user-event
npx ts-jest config:init
```

Add test script in `package.json`:

```json
"scripts": {
  "test": "jest --watchAll",
  "test:ci": "jest --runInBand --coverage"
}
```

### Basic Test Example

```tsx
// src/components/Button.tsx
export default function Button({ onClick, children }: any) {
  return <button onClick={onClick}>{children}</button>;
}

// src/components/Button.test.tsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import Button from './Button';

test('calls handler on click', async () => {
  const user = userEvent.setup();
  const handle = jest.fn();
  render(<Button onClick={handle}>Click</Button>);
  await user.click(screen.getByText('Click'));
  expect(handle).toHaveBeenCalledTimes(1);
});
```

### Testing Hooks

Use helper libraries or custom wrappers.

```bash
npm install -D @testing-library/react-hooks
```

```tsx
import { renderHook, act } from '@testing-library/react-hooks';
import useCounter from './useCounter';

test('increments counter', () => {
  const { result } = renderHook(() => useCounter());
  act(() => result.current.increment());
  expect(result.current.count).toBe(1);
});
```

### Mocking & Stubbing

* Use `jest.mock()` to mock modules.
* Use `vi`/`sinon` if using other test runners.

```ts
jest.mock('../api', () => ({
  fetchUsers: jest.fn().mockResolvedValue([{ id: 1, name: 'Akash' }])
}));
```

---

## 🔗 Integration Testing Patterns

Integration tests combine components and utilities to verify interactions.

Example: testing a component that uses context and router

```tsx
import { render, screen } from '@testing-library/react';
import { MemoryRouter } from 'react-router-dom';
import { AppProvider } from '../context';
import Dashboard from './Dashboard';

render(
  <MemoryRouter>
    <AppProvider>
      <Dashboard />
    </AppProvider>
  </MemoryRouter>
);
expect(screen.getByText(/welcome/i)).toBeInTheDocument();
```

Use `msw` (Mock Service Worker) to mock network requests in integration tests.

---

## 🧭 End-to-End Testing with Cypress

### Setup

```bash
npm install -D cypress
npx cypress open
```

Folder: `cypress/e2e/*.spec.{js,ts}`

### Writing E2E Tests

```js
// cypress/e2e/login.spec.js
describe('Login flow', () => {
  it('logs in successfully', () => {
    cy.visit('/login');
    cy.get('input[name=email]').type('user@example.com');
    cy.get('input[name=password]').type('password');
    cy.get('button[type=submit]').click();
    cy.url().should('include', '/dashboard');
    cy.contains('Welcome');
  });
});
```

### Network Stubbing & Fixtures

```js
cy.intercept('POST', '/api/login', { fixture: 'login-success.json' });
```

Use headless runs in CI: `npx cypress run --headed` or with browsers specified.

---

## 🖼️ Visual Regression Testing (Storybook + Chromatic)

Storybook documents UI components in isolation.

### Setup Storybook

```bash
npx sb init
```

Use **Chromatic** to run visual regression tests and host Storybook snapshots.

```bash
npx chromatic --project-token=$CHROMATIC_TOKEN
```

This flags visual diffs and prevents regressions in components.

---

## 🧾 Test Data & Test Doubles

* **Fixtures**: static JSON data for predictable responses.
* **Factories**: generate test data with libraries like `rosie` or `fishery`.
* **Test doubles**: mocks, stubs, spies – choose based on isolation needs.

Use MSW for realistic API mocking during tests.

---

## ♿ Accessibility Testing (a11y)

Automate accessibility checks in tests using `axe`.

```bash
npm install -D @axe-core/react jest-axe
```

```tsx
import { render } from '@testing-library/react';
import { axe } from 'jest-axe';

test('accessible', async () => {
  const { container } = render(<MyComponent />);
  const results = await axe(container);
  expect(results).toHaveNoViolations();
});
```

Add Lighthouse and axe checks to CI for continuous accessibility monitoring.

---

## ⚡ Performance & Load Testing

For front-end performance and load testing, consider:

* **Lighthouse CI** (audit performance metrics in CI).
* **k6** or **Gatling** for API load testing.
* **WebPageTest** for real-world performance metrics.

Integrate automated Lighthouse runs into CI to guard regressions on FCP, LCP, CLS.

---

## 🛠️ CI/CD Integration (GitHub Actions Example)

Sample `.github/workflows/ci.yml`:

```yaml
name: CI
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [18.x]
    steps:
      - uses: actions/checkout@v4
      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'
      - name: Install
        run: npm ci
      - name: Run lint
        run: npm run lint
      - name: Run unit tests
        run: npm run test:ci
      - name: Run Cypress (headless)
        uses: cypress-io/github-action@v5
        with:
          runTests: true
          browser: chrome
      - name: Upload coverage
        uses: actions/upload-artifact@v4
        with:
          name: coverage-report
          path: coverage
```

### Caching & Parallelization

* Cache `node_modules` or npm cache for faster CI.
* Split jobs into `lint`, `unit`, `e2e` to run in parallel when possible.

### Publishing Artifacts & Reports

* Upload coverage reports, test results, and storybook build as artifacts.
* Post PR comments with coverage delta using `actions/upload-artifact` and comment bots.

---

## ⚠️ Test Flakiness & Reliability

* Flaky tests often come from timing, network dependency, or shared state.
* Use `msw` and fixtures to limit network flakiness.
* Prefer `waitFor` patterns in React Testing Library over arbitrary `cy.wait()`.
* Re-run flaky tests selectively and investigate root cause.

---

## ✅ Best Practices & Checklist

* Keep tests **fast** and **deterministic**.
* Prefer integration & component tests over implementation-heavy unit tests.
* Use MSW for network mocking.
* Run E2E tests selectively (on main branch or nightly).
* Integrate visual and accessibility tests into CI.
* Track coverage, but don’t treat % as the only quality metric.

---

## 🧾 Summary

* Use **Jest + React Testing Library** for unit and component tests.
* Use **Cypress** for robust E2E tests and `msw`/fixtures for stable network mocks.
* Integrate Storybook + Chromatic for visual regression.
* Automate tests, linting, and reports in CI (example GitHub Actions provided).
* Prioritize reliability, speed, and coverage in your test strategy.

---

Next: [Part VIII – Modern React Ecosystem →](#)
