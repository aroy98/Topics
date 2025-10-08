# Node.js Mastery Series

## **Part VI – Testing & CI/CD**

### **1. Overview**

Testing and CI/CD ensure your Node.js application remains **stable**, **reliable**, and **ready for production**. This section covers **unit testing**, **integration testing**, **mocking**, and **automated CI/CD pipelines**.

---

### **2. Unit Testing with Jest**

**Jest** is the most popular testing framework for Node.js.

#### **Installation:**

```bash
npm install --save-dev jest
```

#### **Configuration:**

Add to `package.json`:

```json
"scripts": {
  "test": "jest"
}
```

#### **Example Test (sum.test.js):**

```js
import { sum } from './sum.js';

test('adds 1 + 2 to equal 3', () => {
  expect(sum(1, 2)).toBe(3);
});
```

#### **Example Function (sum.js):**

```js
export const sum = (a, b) => a + b;
```

Run tests:

```bash
npm test
```

---

### **3. Testing with Mocha & Chai**

Alternative lightweight framework for backend testing.

#### **Setup:**

```bash
npm install --save-dev mocha chai
```

#### **Example:**

```js
import { expect } from 'chai';
import { add } from '../math.js';

describe('Math tests', () => {
  it('should add numbers correctly', () => {
    expect(add(2, 3)).to.equal(5);
  });
});
```

Run with:

```bash
npx mocha
```

---

### **4. Integration Tests with Supertest**

Used to test API endpoints.

#### **Setup:**

```bash
npm install --save-dev supertest
```

#### **Example:**

```js
import request from 'supertest';
import app from '../server.js';

describe('GET /users', () => {
  it('should return users', async () => {
    const res = await request(app).get('/users');
    expect(res.status).toBe(200);
    expect(res.body).toBeInstanceOf(Array);
  });
});
```

---

### **5. Test Database Setup**

Use in-memory or isolated databases for testing.

#### **MongoDB Memory Server:**

```bash
npm install --save-dev mongodb-memory-server
```

```js
import { MongoMemoryServer } from 'mongodb-memory-server';
import mongoose from 'mongoose';

beforeAll(async () => {
  const mongoServer = await MongoMemoryServer.create();
  await mongoose.connect(mongoServer.getUri());
});

afterAll(async () => {
  await mongoose.disconnect();
});
```

---

### **6. Mocking External Services**

Mock APIs or DB calls to isolate logic.

#### **Example:**

```js
jest.mock('../services/emailService', () => ({
  sendEmail: jest.fn(() => true)
}));
```

---

### **7. Continuous Integration (CI) Overview**

CI automatically runs tests, lints code, and builds apps whenever changes are pushed.

**Common CI tools:**

* GitHub Actions
* Jenkins
* GitLab CI
* CircleCI

---

### **8. CI/CD with GitHub Actions**

#### **Workflow Example (.github/workflows/node.yml):**

```yaml
name: Node.js CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [18.x, 20.x]

    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        run: npm test

      - name: Build project
        run: npm run build
```

---

### **9. CI/CD with Jenkins**

Use **Jenkins pipelines** to automate builds and deployments.

#### **Example Jenkinsfile:**

```groovy
pipeline {
  agent any
  stages {
    stage('Install') {
      steps {
        sh 'npm install'
      }
    }
    stage('Test') {
      steps {
        sh 'npm test'
      }
    }
    stage('Build') {
      steps {
        sh 'npm run build'
      }
    }
    stage('Deploy') {
      steps {
        echo 'Deploying application...'
      }
    }
  }
}
```

---

### **10. Best Practices for CI/CD Pipelines**

* Run tests in **parallel** to save time.
* Use **separate environments** (dev, staging, prod).
* Maintain **versioned builds**.
* Integrate **code coverage** with `jest --coverage`.
* Automate **security scanning** using `npm audit` or Snyk.
* Deploy automatically after successful builds.

---

### **11. Test Coverage Reporting**

Generate reports for visibility.

```bash
npm install --save-dev jest-coverage-badges
npm test -- --coverage
```

---

### **12. Continuous Deployment Example (Vercel / AWS / Docker)**

#### **Using Docker in CI/CD:**

```Dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

Add to Jenkins or GitHub Action deployment steps:

```bash
docker build -t nodejs-app .
docker run -d -p 3000:3000 nodejs-app
```

---

### **13. Sample CI/CD Flow**

1. Developer pushes code to GitHub.
2. GitHub Action runs build + tests.
3. On success → Docker image is built.
4. Image deployed to AWS ECS / Kubernetes.
5. Jenkins monitors health & sends alerts.

---

### **14. Summary**

* Jest for **unit testing**.
* Supertest for **API integration testing**.
* GitHub Actions / Jenkins for **CI/CD**.
* Docker for **consistent deployment**.

---

## **Next Up: Part VII – Advanced Node Concepts**
