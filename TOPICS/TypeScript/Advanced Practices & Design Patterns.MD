# 🧠 TypeScript Mastery Series – Part X: Advanced Practices & Design Patterns

---

## 📘 Table of Contents

1. [Introduction](#introduction)
2. [Type-Driven Development](#type-driven-development)
3. [Domain Modeling with Types](#domain-modeling-with-types)
4. [Declarative vs Imperative Typing](#declarative-vs-imperative-typing)
5. [Applying Design Patterns in TypeScript](#applying-design-patterns-in-typescript)

   * [Singleton Pattern](#singleton-pattern)
   * [Factory Pattern](#factory-pattern)
   * [Strategy Pattern](#strategy-pattern)
   * [Observer Pattern](#observer-pattern)
   * [Decorator Pattern](#decorator-pattern)
6. [Advanced Inference and Type Composition](#advanced-inference-and-type-composition)
7. [Best Practices for Large-Scale Projects](#best-practices-for-large-scale-projects)
8. [Summary](#summary)

---

## 🧠 Introduction

This final part of the TypeScript Mastery Series focuses on **advanced type-level programming** and **object-oriented design patterns**. You’ll learn how to apply design principles with TypeScript’s strong type system for maintainable and scalable codebases.

---

## 🧩 Type-Driven Development

Type-driven development (TDD with types) focuses on designing robust types before writing actual logic.

### Example

```ts
type User = {
  id: string;
  name: string;
  email: string;
  verified: boolean;
};

function verifyUser(user: User): User {
  return { ...user, verified: true };
}
```

Here, the **types define the domain contracts** before implementation begins, reducing logical errors early.

---

## 🏗️ Domain Modeling with Types

Domain modeling helps describe real-world entities using TypeScript’s types and interfaces.

### Example: Banking Domain

```ts
type AccountType = 'savings' | 'current';

type Account = {
  id: string;
  holderName: string;
  type: AccountType;
  balance: number;
};

function deposit(account: Account, amount: number): Account {
  return { ...account, balance: account.balance + amount };
}
```

### Benefits:

* Enforces domain boundaries
* Prevents invalid state
* Improves refactoring safety

---

## ⚖️ Declarative vs Imperative Typing

TypeScript allows you to describe logic declaratively with types rather than imperative code.

### Imperative Example

```ts
function getFirst(arr: any[]) {
  return arr[0];
}
```

### Declarative Example

```ts
function getFirst<T>(arr: T[]): T {
  return arr[0];
}
```

Declarative typing with generics leads to safer and reusable code.

---

## 🧩 Applying Design Patterns in TypeScript

TypeScript enhances classical design patterns by enforcing strong type rules.

---

### 🧍‍♂️ Singleton Pattern

Ensures only one instance of a class exists.

```ts
class Database {
  private static instance: Database;

  private constructor() {}

  static getInstance(): Database {
    if (!Database.instance) {
      Database.instance = new Database();
    }
    return Database.instance;
  }
}

const db1 = Database.getInstance();
const db2 = Database.getInstance();
console.log(db1 === db2); // true
```

---

### 🏭 Factory Pattern

Creates objects without exposing instantiation logic.

```ts
interface Car {
  drive(): void;
}

class Sedan implements Car {
  drive() {
    console.log('Driving a Sedan');
  }
}

class SUV implements Car {
  drive() {
    console.log('Driving an SUV');
  }
}

class CarFactory {
  static createCar(type: 'sedan' | 'suv'): Car {
    if (type === 'sedan') return new Sedan();
    return new SUV();
  }
}

const myCar = CarFactory.createCar('suv');
myCar.drive();
```

---

### ⚙️ Strategy Pattern

Defines a family of algorithms and makes them interchangeable.

```ts
interface PaymentStrategy {
  pay(amount: number): void;
}

class PayPalPayment implements PaymentStrategy {
  pay(amount: number) {
    console.log(`Paid $${amount} using PayPal`);
  }
}

class CreditCardPayment implements PaymentStrategy {
  pay(amount: number) {
    console.log(`Paid $${amount} using Credit Card`);
  }
}

class PaymentContext {
  constructor(private strategy: PaymentStrategy) {}

  setStrategy(strategy: PaymentStrategy) {
    this.strategy = strategy;
  }

  executePayment(amount: number) {
    this.strategy.pay(amount);
  }
}

const context = new PaymentContext(new PayPalPayment());
context.executePayment(100);
context.setStrategy(new CreditCardPayment());
context.executePayment(250);
```

---

### 👀 Observer Pattern

Defines a one-to-many dependency between objects so when one changes, others are notified.

```ts
interface Observer {
  update(data: string): void;
}

class Subject {
  private observers: Observer[] = [];

  subscribe(observer: Observer) {
    this.observers.push(observer);
  }

  unsubscribe(observer: Observer) {
    this.observers = this.observers.filter(o => o !== observer);
  }

  notify(data: string) {
    this.observers.forEach(o => o.update(data));
  }
}

class ConcreteObserver implements Observer {
  constructor(private name: string) {}
  update(data: string) {
    console.log(`${this.name} received: ${data}`);
  }
}

const subject = new Subject();
const obs1 = new ConcreteObserver('Observer 1');
const obs2 = new ConcreteObserver('Observer 2');

subject.subscribe(obs1);
subject.subscribe(obs2);
subject.notify('New event occurred!');
```

---

### 🎨 Decorator Pattern

Adds new behavior to objects dynamically.

```ts
interface Coffee {
  cost(): number;
  description(): string;
}

class SimpleCoffee implements Coffee {
  cost() {
    return 5;
  }
  description() {
    return 'Simple Coffee';
  }
}

class MilkDecorator implements Coffee {
  constructor(private coffee: Coffee) {}
  cost() {
    return this.coffee.cost() + 2;
  }
  description() {
    return `${this.coffee.description()} + Milk`;
  }
}

const myCoffee = new MilkDecorator(new SimpleCoffee());
console.log(myCoffee.description()); // Simple Coffee + Milk
console.log(myCoffee.cost()); // 7
```

---

## 🧬 Advanced Inference and Type Composition

TypeScript’s advanced inference allows building powerful type transformations.

### Example: Extracting Return Type

```ts
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

function getUser() {
  return { id: 1, name: 'Akash' };
}

type User = ReturnType<typeof getUser>; // { id: number; name: string }
```

### Example: Deep Partial

```ts
type DeepPartial<T> = {
  [K in keyof T]?: T[K] extends object ? DeepPartial<T[K]> : T[K];
};

interface Project {
  id: number;
  name: string;
  details: {
    budget: number;
    duration: string;
  };
}

const project: DeepPartial<Project> = {
  details: { budget: 10000 }
};
```

---

## 🧱 Best Practices for Large-Scale Projects

✅ Use **strict mode** and never disable type checks.
✅ Prefer **composition** over inheritance for flexibility.
✅ Create **type-safe domain models** before implementing logic.
✅ Use **utility types** (`Partial`, `Pick`, `Omit`) for cleaner code.
✅ Keep **interfaces** and **types** centralized in `/types` folder.
✅ Implement **design patterns** where they simplify code—not just for theory.
✅ Leverage **type inference** to reduce redundancy.

---

## 🧾 Summary

* TypeScript supports **classic design patterns** like Singleton, Factory, Strategy, Observer, and Decorator.
* Use **type-driven development** for predictable, maintainable applications.
* Apply **domain modeling** to align code with business rules.
* Prefer **composition**, **generics**, and **inference** to build scalable systems.
* Advanced type utilities allow declarative, type-safe architecture.

---

🎉 **Congratulations!** You’ve completed the **TypeScript Mastery Series (Parts I–X)** — from fundamentals to advanced design patterns. You’re now fully equipped to build production-grade TypeScript applications with confidence.
