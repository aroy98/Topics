# 🧠 TypeScript Mastery Series – Part II: Functions & Generics

---

## 📘 Table of Contents

1. [Function Basics](#function-basics)
2. [Function Types](#function-types)
3. [Optional, Default, and Rest Parameters](#optional-default-and-rest-parameters)
4. [Function Overloading](#function-overloading)
5. [Generics – Introduction](#generics--introduction)
6. [Generic Functions](#generic-functions)
7. [Generic Constraints](#generic-constraints)
8. [Generic Interfaces & Classes](#generic-interfaces--classes)
9. [Utility Types](#utility-types)
10. [Summary](#summary)

---

## ⚡ Function Basics

Functions in TypeScript can have **typed parameters** and **typed return values**.

```ts
function greet(name: string): string {
  return `Hello, ${name}!`;
}

console.log(greet("Akash"));
```

If no return type is specified, TypeScript will **infer** it.

```ts
function add(a: number, b: number) {
  return a + b; // inferred as number
}
```

---

## 🎯 Function Types

You can define the **function type** separately to enforce consistency.

```ts
type Add = (a: number, b: number) => number;

const add: Add = (x, y) => x + y;
```

Or using an **interface**:

```ts
interface Printer {
  (message: string): void;
}

const printMessage: Printer = (msg) => console.log(msg);
```

---

## ⚙️ Optional, Default, and Rest Parameters

### Optional Parameters

Marked with a `?`, they can be omitted when calling.

```ts
function logMessage(message: string, userId?: string) {
  console.log(message, userId || "Anonymous");
}
logMessage("System started");
```

### Default Parameters

Provide fallback values.

```ts
function discount(price: number, rate: number = 0.1): number {
  return price - price * rate;
}
console.log(discount(100)); // 90
```

### Rest Parameters

Used to handle variable number of arguments.

```ts
function sum(...numbers: number[]): number {
  return numbers.reduce((acc, n) => acc + n, 0);
}
console.log(sum(10, 20, 30)); // 60
```

---

## 🔀 Function Overloading

You can define multiple function signatures for a single implementation.

```ts
function format(value: string): string;
function format(value: number): string;
function format(value: string | number): string {
  return typeof value === "string" ? value.toUpperCase() : value.toFixed(2);
}

console.log(format("typescript")); // TYPESCRIPT
console.log(format(3.14159));      // 3.14
```

---

## 🧬 Generics – Introduction

Generics allow you to **write reusable, type-safe functions** that work with multiple data types.

```ts
function identity<T>(value: T): T {
  return value;
}

console.log(identity<string>("Hello"));
console.log(identity<number>(42));
```

The `<T>` is a **type variable** — it acts like a placeholder for a real type.

---

## 🧩 Generic Functions

Generics help maintain type safety and flexibility.

```ts
function merge<T, U>(a: T, b: U): T & U {
  return { ...a, ...b };
}

const person = merge({ name: "Alice" }, { age: 30 });
console.log(person.name); // "Alice"
console.log(person.age);  // 30
```

---

## 🔒 Generic Constraints

You can **restrict** what types are allowed using `extends`.

```ts
function getLength<T extends { length: number }>(item: T): number {
  return item.length;
}

console.log(getLength("Hello"));
console.log(getLength([1, 2, 3]));
// console.log(getLength(10)); ❌ Error: number doesn’t have a length property
```

---

## 🧱 Generic Interfaces & Classes

### Generic Interface

```ts
interface Repository<T> {
  add(item: T): void;
  getAll(): T[];
}

class MemoryRepo<T> implements Repository<T> {
  private data: T[] = [];
  
  add(item: T): void {
    this.data.push(item);
  }

  getAll(): T[] {
    return this.data;
  }
}

const repo = new MemoryRepo<string>();
repo.add("First");
console.log(repo.getAll());
```

### Generic Class Example

```ts
class Pair<K, V> {
  constructor(public key: K, public value: V) {}
}

const pair = new Pair<number, string>(1, "TypeScript");
console.log(pair.key, pair.value);
```

---

## 🧰 Utility Types

TypeScript provides built-in **utility types** that make working with types easier.

| Utility        | Description                                       | Example                  |          |
| -------------- | ------------------------------------------------- | ------------------------ | -------- |
| `Partial<T>`   | Makes all properties optional                     | `Partial<User>`          |          |
| `Required<T>`  | Makes all properties required                     | `Required<User>`         |          |
| `Readonly<T>`  | Makes all properties read-only                    | `Readonly<User>`         |          |
| `Pick<T, K>`   | Selects specific keys                             | `Pick<User, "id"         | "name">` |
| `Omit<T, K>`   | Excludes specific keys                            | `Omit<User, "password">` |          |
| `Record<K, T>` | Creates a type with keys of `K` and values of `T` | `Record<string, number>` |          |

### Example:

```ts
interface User {
  id: number;
  name: string;
  email: string;
}

type UserPreview = Pick<User, "id" | "name">;

const user: UserPreview = { id: 1, name: "John" };
```

---

## 🗾 Summary

* Functions can have typed parameters, optional and default values.
* Function **overloading** enables multiple signatures.
* **Generics** allow reusable, type-safe code for multiple data types.
* Use **generic constraints** to ensure valid type properties.
* Built-in **utility types** simplify transformations of existing types.

---

Next: [Part III – Object-Oriented TypeScript ➔](#)
