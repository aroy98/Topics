# Part X — Architecture & System Patterns

**Microservices, Frontend Architecture, State Management, Reactive Systems, and Security**

---

## Preface

As JavaScript has grown beyond the browser into backend systems, mobile applications, and even edge computing, understanding **software architecture** and **design patterns** has become essential.

This section explores how JavaScript fits into modern architectures such as **microservices**, **frontend modularization**, **state management**, and **reactive event-driven systems**. It also includes vital **security practices** and **internationalization (i18n)** considerations for production-grade systems.

---

## Table of Contents

1. [Microservices & API Architecture](#microservices--api-architecture)
2. [Serverless Patterns](#serverless-patterns)
3. [Frontend Architecture](#frontend-architecture)
4. [State Management Patterns](#state-management-patterns)
5. [Reactive & Event-Driven Systems](#reactive--event-driven-systems)
6. [Security Patterns](#security-patterns)
7. [Internationalization & Accessibility](#internationalization--accessibility)
8. [Summary](#summary)

---

## 1. Microservices & API Architecture

Microservices decompose an application into **independent, deployable services** communicating over APIs.

### Benefits

* Scalability and fault isolation
* Technology-agnostic flexibility
* Independent deployments

### Example Structure

```
User Service → handles authentication
Product Service → manages product catalog
Order Service → processes and tracks orders
API Gateway → routes and secures traffic
```

### Common Tools

* **Express.js / Fastify** — API frameworks
* **GraphQL** — Query-based API layer
* **Docker + Kubernetes** — Deployment orchestration
* **Redis / NATS / RabbitMQ** — Message queues

### Example (Express.js API)

```js
const express = require('express');
const app = express();

app.get('/api/products', (req, res) => {
  res.json([{ id: 1, name: 'Laptop' }]);
});

app.listen(3000, () => console.log('Service running on port 3000'));
```

---

## 2. Serverless Patterns

Serverless functions allow developers to deploy code without managing infrastructure.

### Characteristics

* Event-driven execution
* Auto-scaling and pay-per-use model
* Stateless and ephemeral

### Example (AWS Lambda)

```js
exports.handler = async (event) => {
  return { statusCode: 200, body: 'Hello from Lambda!' };
};
```

### Platforms

* **AWS Lambda**
* **Google Cloud Functions**
* **Azure Functions**
* **Cloudflare Workers / Vercel Edge Functions**

---

## 3. Frontend Architecture

Modern frontend architectures emphasize modularity, scalability, and maintainability.

### Patterns

* **Component-based architecture** (React, Vue, Angular)
* **Micro-frontends** — Independent frontends integrated dynamically
* **Monorepo management** using Nx or Turborepo

### Example Structure (React)

```
components/
  ├── Navbar.js
  ├── ProductCard.js
  └── Footer.js
pages/
  ├── Home.js
  └── Product.js
context/
  └── AuthContext.js
```

### Micro-Frontend Example

Using **Module Federation (Webpack 5)**:

```js
// host app webpack.config.js
new ModuleFederationPlugin({
  name: 'host',
  remotes: {
    productApp: 'productApp@http://localhost:8081/remoteEntry.js'
  }
});
```

---

## 4. State Management Patterns

Managing shared state is crucial in large applications.

| Pattern          | Description                 | Example Libraries         |
| ---------------- | --------------------------- | ------------------------- |
| **Flux**         | One-way data flow           | Redux                     |
| **Observer**     | Reactive subscriptions      | RxJS                      |
| **Atomic State** | Scoped stores per component | Recoil, Jotai             |
| **Signal-based** | Change detection            | Angular Signals, Solid.js |

### Example (Redux)

```js
function reducer(state = { count: 0 }, action) {
  switch (action.type) {
    case 'INCREMENT':
      return { count: state.count + 1 };
    default:
      return state;
  }
}
```

### Example (RxJS)

```js
import { BehaviorSubject } from 'rxjs';
const counter$ = new BehaviorSubject(0);
counter$.subscribe(v => console.log(v));
counter$.next(1);
```

---

## 5. Reactive & Event-Driven Systems

Reactive systems focus on **responsiveness**, **resilience**, and **asynchronous communication**.

### Example: Event Emitter (Node.js)

```js
const EventEmitter = require('events');
const emitter = new EventEmitter();

emitter.on('order', (data) => {
  console.log('Order received:', data);
});

emitter.emit('order', { id: 1, item: 'Book' });
```

### Message Queues

Use queues to decouple producers and consumers.

| Queue             | Language             | Use Case             |
| ----------------- | -------------------- | -------------------- |
| **RabbitMQ**      | AMQP                 | Enterprise messaging |
| **Kafka**         | Java / Node bindings | Event streams        |
| **Redis Streams** | JS-friendly          | Lightweight pub/sub  |

---

## 6. Security Patterns

Security must be integrated at every level of design.

### Common Threats & Mitigations

| Threat             | Description                | Mitigation                    |
| ------------------ | -------------------------- | ----------------------------- |
| **XSS**            | Injected scripts           | Input sanitization, CSP       |
| **CSRF**           | Cross-site request forgery | CSRF tokens, SameSite cookies |
| **SQL Injection**  | Query manipulation         | Parameterized queries         |
| **Insecure JWTs**  | Forged tokens              | Use HMAC/RS256, short expiry  |
| **CORS Misconfig** | Resource access leaks      | Define allowed origins        |

### Secure Coding Practices

* Always validate input and sanitize output.
* Use HTTPS everywhere.
* Implement rate limiting and throttling.
* Encrypt sensitive data at rest and in transit.

---

## 7. Internationalization & Accessibility

### Internationalization (i18n)

* Use libraries like **i18next**, **FormatJS**, or **React-Intl**.
* Externalize text, dates, and currency formats.

```js
import i18n from 'i18next';
i18n.init({
  lng: 'en',
  resources: { en: { translation: { hello: 'Hello World' } } }
});
```

### Accessibility (a11y)

* Provide ARIA labels and roles.
* Maintain color contrast ratios.
* Enable keyboard navigation.

Example:

```html
<button aria-label="Add to Cart">🛒</button>
```

---

## 8. Summary

| Concept                   | Description                                     |
| ------------------------- | ----------------------------------------------- |
| **Microservices**         | Modular backend services communicating via APIs |
| **Serverless**            | Event-driven, managed compute functions         |
| **Frontend Architecture** | Component-based modular design                  |
| **State Management**      | Flux, Redux, Observer, Signals                  |
| **Reactive Systems**      | Event-driven, asynchronous communication        |
| **Security**              | XSS, CSRF, and authentication protections       |
| **Internationalization**  | Multilingual and accessible design              |

---

**Next Section:**
🧩 *Part XI — Performance & Optimization (Engine-level and Runtime Techniques)*
