# 🧩 TypeScript Mastery Series – Part I: Fundamentals

---

## 📘 Table of Contents

1. [Introduction to TypeScript](#introduction-to-typescript)
2. [Setting Up TypeScript](#setting-up-typescript)
3. [Type Inference and Type Annotations](#type-inference-and-type-annotations)
4. [Primitive and Complex Types](#primitive-and-complex-types)
5. [Union, Intersection, and Literal Types](#union-intersection-and-literal-types)
6. [Type Aliases and Interfaces](#type-aliases-and-interfaces)
7. [Enums](#enums)
8. [Type Casting](#type-casting)
9. [Type Narrowing](#type-narrowing)
10. [`unknown`, `any`, and `never`](#unknown-any-and-never)
11. [Summary](#summary)

---

## 🧠 Introduction to TypeScript

**TypeScript** is a *typed superset of JavaScript* developed by Microsoft.
It compiles to plain JavaScript and adds **static typing**, **interfaces**, and **type inference** — helping you catch bugs during development instead of runtime.

### 🔍 Why Use TypeScript?

* Early detection of bugs through static analysis
* Rich tooling support (IntelliSense, autocomplete, refactoring)
* Better maintainability in large projects
* Strong integration with modern frameworks (React, Node, Angular)

---

## ⚙️ Setting Up TypeScript

You can install and run TypeScript globally or locally in your project.

### 🧬 Global installation:

```bash
npm install -g typescript
```

### 📦 Local project setup:

```bash
mkdir ts-demo && cd ts-demo
npm init -y
npm install typescript --save-dev
npx tsc --init
```

This creates a `tsconfig.json` file that defines your project’s TypeScript configuration.

### Example `tsconfig.json`

```json
{
  "compilerOptions": {
    "target": "es2022",
    "module": "commonjs",
    "strict": true,
    "outDir": "./dist",
    "rootDir": "./src",
    "esModuleInterop": true
  }
}
```

---

## 🗾 Type Inference and Type Annotations

TypeScript can **infer** the type of a variable, but you can also **explicitly declare** it.

### Example: Type Annotation

```ts
let name: string = "Akash";
let age: number = 30;
let isDeveloper: boolean = true;
```

### Example: Type Inference

```ts
let city = "Mumbai"; // inferred as string
let count = 100;     // inferred as number
```

If you try to assign a different type:

```ts
city = 123; // ❌ Error: Type 'number' is not assignable to type 'string'
```

---

## 🧱 Primitive and Complex Types

### Primitive Types

* `string`
* `number`
* `boolean`
* `null`
* `undefined`
* `symbol`
* `bigint`

```ts
let title: string = "Engineer";
let salary: number = 50000;
let isActive: boolean = true;
```

### Complex Types

* `Array`
* `Tuple`
* `Object`

```ts
let scores: number[] = [90, 80, 85]; // array of numbers
let user: [string, number] = ["John", 30]; // tuple
let person: { name: string; age: number } = { name: "Alice", age: 25 };
```

---

## 🔗 Union, Intersection, and Literal Types

### Union Types

Allow a variable to hold more than one type.

```ts
let input: string | number;
input = "Hello";
input = 42;
```

### Intersection Types

Combine multiple types into one.

```ts
type Person = { name: string };
type Contact = { email: string };
type Employee = Person & Contact;

const emp: Employee = { name: "Ravi", email: "ravi@company.com" };
```

### Literal Types

Restrict variable values to a specific literal.

```ts
let direction: "up" | "down" | "left" | "right";
direction = "up"; // ✅
direction = "north"; // ❌ Error
```

---

## 🧩 Type Aliases and Interfaces

### Type Alias

Defines a reusable custom type.

```ts
type ID = string | number;
type User = {
  id: ID;
  name: string;
  active: boolean;
};
```

### Interface

Defines the shape of an object and supports extension.

```ts
interface UserProfile {
  name: string;
  age: number;
  email?: string; // optional
}

interface Admin extends UserProfile {
  role: "admin" | "superadmin";
}

const admin: Admin = { name: "Sara", age: 29, role: "admin" };
```

---

## 🧮 Enums

Enums represent a group of named constants.

### Numeric Enum

```ts
enum Direction {
  Up,
  Down,
  Left,
  Right
}
console.log(Direction.Up); // 0
```

### String Enum

```ts
enum Status {
  Success = "SUCCESS",
  Failure = "FAILURE"
}
console.log(Status.Success); // "SUCCESS"
```

---

## 🪄 Type Casting

Used to tell TypeScript the expected type of a variable when it can’t infer it.

```ts
let someValue: unknown = "Hello TypeScript!";
let strLength = (someValue as string).length;

console.log(strLength); // 16
```

Alternate syntax:

```ts
let strLength2 = (<string>someValue).length;
```

---

## 🔍 Type Narrowing

TypeScript can narrow down the type of a variable based on control flow.

```ts
function printId(id: string | number) {
  if (typeof id === "string") {
    console.log(id.toUpperCase());
  } else {
    console.log(id.toFixed(2));
  }
}
```

You can also narrow with:

* `instanceof`
* `in`
* equality checks

---

## 📄 `unknown`, `any`, and `never`

### `any`

Turns off type checking – avoid using unless absolutely needed.

```ts
let value: any = 10;
value = "text";
value = true; // no error
```

### `unknown`

Safer alternative to `any` – you must type check before using it.

```ts
let data: unknown = "Hello";
if (typeof data === "string") {
  console.log(data.toUpperCase());
}
```

### `never`

Used for values that should never occur (e.g. in exhaustive checks).

```ts
function throwError(message: string): never {
  throw new Error(message);
}
```

---

## 📈 Summary

* TypeScript adds **strong static typing** and **developer tooling** on top of JavaScript.
* Use **type inference** for simplicity and **type annotations** for clarity.
* Master **union**, **intersection**, and **literal types** for flexibility.
* Prefer **interfaces** for object contracts, **type aliases** for flexibility.
* Always use **strict mode** in `tsconfig.json` for better reliability.

---

Next: [Part II – Functions & Generics ➔](#)
