# 🏗️ TypeScript Mastery Series – Part III: Object-Oriented TypeScript

---

## 📘 Table of Contents

1. [Introduction to Object-Oriented Programming in TypeScript](#introduction-to-object-oriented-programming-in-typescript)
2. [Classes and Constructors](#classes-and-constructors)
3. [Access Modifiers](#access-modifiers)
4. [Readonly and Static Properties](#readonly-and-static-properties)
5. [Inheritance](#inheritance)
6. [Abstract Classes](#abstract-classes)
7. [Interfaces vs Abstract Classes](#interfaces-vs-abstract-classes)
8. [Implementing Interfaces](#implementing-interfaces)
9. [Getters and Setters](#getters-and-setters)
10. [Mixins and Composition Patterns](#mixins-and-composition-patterns)
11. [Summary](#summary)

---

## 🧠 Introduction to Object-Oriented Programming in TypeScript

Object-Oriented Programming (OOP) in TypeScript extends JavaScript’s prototype-based model with **classes, inheritance, access modifiers**, and **interfaces** for type safety.

**Core OOP Concepts:**

* **Encapsulation:** Hide internal state using `private` or `protected`.
* **Inheritance:** Reuse functionality via `extends`.
* **Polymorphism:** Define methods that behave differently in subclasses.
* **Abstraction:** Define structure without implementation using `abstract`.

---

## 🏗️ Classes and Constructors

A **class** defines a blueprint for creating objects.

```ts
class Person {
  name: string;
  age: number;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }

  greet(): void {
    console.log(`Hello, my name is ${this.name}`);
  }
}

const person = new Person("Akash", 30);
person.greet(); // Hello, my name is Akash
```

### Parameter Properties

You can shorten property declaration and initialization:

```ts
class Employee {
  constructor(public id: number, public name: string) {}
}

const emp = new Employee(1, "Alice");
console.log(emp.name); // Alice
```

---

## 🔒 Access Modifiers

| Modifier    | Description                       | Accessible From           |
| ----------- | --------------------------------- | ------------------------- |
| `public`    | Default – accessible everywhere   | Class, subclass, instance |
| `private`   | Only within the same class        | Class only                |
| `protected` | Same class and derived subclasses | Class + Subclass          |

```ts
class User {
  private password: string;
  protected role: string;

  constructor(public name: string, password: string, role: string) {
    this.password = password;
    this.role = role;
  }
}

class Admin extends User {
  showRole() {
    console.log(this.role); // ✅ accessible
    // console.log(this.password); // ❌ Error
  }
}
```

---

## 🧱 Readonly and Static Properties

### Readonly

```ts
class Config {
  readonly appName: string = "MyApp";
}

const c = new Config();
// c.appName = "NewApp"; // ❌ Error
```

### Static

Static members belong to the **class itself**, not instances.

```ts
class Counter {
  static count: number = 0;

  static increment() {
    Counter.count++;
  }
}

Counter.increment();
console.log(Counter.count); // 1
```

---

## 🧬 Inheritance

A class can extend another class using `extends`.

```ts
class Animal {
  constructor(public name: string) {}
  move(distance: number) {
    console.log(`${this.name} moved ${distance}m.`);
  }
}

class Dog extends Animal {
  bark() {
    console.log(`${this.name} says: Woof!`);
  }
}

const dog = new Dog("Buddy");
dog.bark();
dog.move(10);
```

### Method Overriding

```ts
class Bird extends Animal {
  move(distance: number) {
    console.log(`${this.name} is flying ${distance}m high.`);
  }
}

const eagle = new Bird("Eagle");
eagle.move(100);
```

---

## 🧩 Abstract Classes

Abstract classes can’t be instantiated directly — they define **common structure** for derived classes.

```ts
abstract class Shape {
  abstract getArea(): number;
  abstract getPerimeter(): number;
}

class Circle extends Shape {
  constructor(public radius: number) {
    super();
  }

  getArea(): number {
    return Math.PI * this.radius ** 2;
  }

  getPerimeter(): number {
    return 2 * Math.PI * this.radius;
  }
}

const circle = new Circle(5);
console.log(circle.getArea());
```

---

## ⚖️ Interfaces vs Abstract Classes

| Feature              | Interface                | Abstract Class                  |
| -------------------- | ------------------------ | ------------------------------- |
| Implementation       | None (only declarations) | Can have partial implementation |
| Multiple Inheritance | ✅ Yes                    | ❌ No                            |
| Fields               | ❌                        | ✅                               |
| Use Case             | Define *contract*        | Define *base behavior*          |

Example:

```ts
interface Drawable {
  draw(): void;
}

abstract class GraphicObject implements Drawable {
  abstract draw(): void;
  move(x: number, y: number) {
    console.log(`Moved to (${x}, ${y})`);
  }
}
```

---

## ⚙️ Implementing Interfaces

Classes can **implement** one or more interfaces.

```ts
interface Logger {
  log(message: string): void;
}

interface ErrorHandler {
  handleError(error: string): void;
}

class AppLogger implements Logger, ErrorHandler {
  log(message: string): void {
    console.log(`LOG: ${message}`);
  }

  handleError(error: string): void {
    console.error(`ERROR: ${error}`);
  }
}

const logger = new AppLogger();
logger.log("System started");
logger.handleError("Disk full");
```

---

## 🧮 Getters and Setters

Encapsulate access to private fields.

```ts
class BankAccount {
  private _balance: number = 0;

  get balance(): number {
    return this._balance;
  }

  set balance(amount: number) {
    if (amount < 0) throw new Error("Negative balance not allowed");
    this._balance = amount;
  }
}

const account = new BankAccount();
account.balance = 1000;
console.log(account.balance); // 1000
```

---

## 🧩 Mixins and Composition Patterns

Mixins allow combining behaviors from multiple classes.

```ts
type Constructor<T = {}> = new (...args: any[]) => T;

function CanFly<TBase extends Constructor>(Base: TBase) {
  return class extends Base {
    fly() {
      console.log("Flying high!");
    }
  };
}

class Vehicle {
  move() {
    console.log("Moving on road...");
  }
}

class FlyingCar extends CanFly(Vehicle) {}

const car = new FlyingCar();
car.move(); // Moving on road...
car.fly();  // Flying high!
```

---

## 🧾 Summary

* Classes in TypeScript provide structure with **constructors** and **methods**.
* Use **access modifiers** to control visibility (`public`, `private`, `protected`).
* **Inheritance** and **method overriding** help reuse logic.
* **Abstract classes** define common patterns for subclasses.
* **Interfaces** define contracts; **abstract classes** define partial behavior.
* **Mixins** allow flexible composition beyond single inheritance.

---

Next: [Part IV – Advanced Types →](#)
