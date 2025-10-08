# 🧩 TypeScript Mastery Series – Part IV: Advanced Types

---

## 📘 Table of Contents

1. [Introduction](#introduction)
2. [Type Aliases vs Interfaces](#type-aliases-vs-interfaces)
3. [Union and Intersection Types](#union-and-intersection-types)
4. [Discriminated Unions](#discriminated-unions)
5. [Mapped Types](#mapped-types)
6. [Conditional Types](#conditional-types)
7. [Template Literal Types](#template-literal-types)
8. [Indexed Access Types](#indexed-access-types)
9. [Key Remapping in Mapped Types](#key-remapping-in-mapped-types)
10. [Infer Keyword](#infer-keyword)
11. [Recursive Type Definitions](#recursive-type-definitions)
12. [Summary](#summary)

---

## 🧠 Introduction

Advanced types in TypeScript allow you to build highly flexible and reusable type systems. They provide **type-level logic**, **transformations**, and **composition** capabilities for complex real-world applications.

---

## 🧾 Type Aliases vs Interfaces

Both define the shape of data, but differ slightly in flexibility.

### Type Alias

```ts
type User = {
  id: number;
  name: string;
};
```

### Interface

```ts
interface IUser {
  id: number;
  name: string;
}
```

### Differences

| Feature             | Type Alias                        | Interface                |
| ------------------- | --------------------------------- | ------------------------ |
| Extending           | Using intersection `&`            | Using `extends`          |
| Declaration merging | ❌ No                              | ✅ Yes                    |
| Use cases           | Complex types, primitives, unions | Object shape definitions |

Example:

```ts
interface A { name: string }
interface A { age: number }
const user: A = { name: "Akash", age: 30 }; // Declaration merging
```

---

## 🔗 Union and Intersection Types

### Union Type

Allows multiple possible types.

```ts
type Status = "success" | "error" | "loading";
```

### Intersection Type

Combines multiple types.

```ts
type Person = { name: string };
type Contact = { email: string };
type Employee = Person & Contact;
```

Example:

```ts
const emp: Employee = { name: "Ravi", email: "ravi@mail.com" };
```

---

## ⚖️ Discriminated Unions

Used to model multiple possible types with a shared discriminator field.

```ts
type Circle = { kind: "circle"; radius: number };
type Square = { kind: "square"; side: number };
type Shape = Circle | Square;

function getArea(shape: Shape): number {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.side ** 2;
  }
}
```

---

## 🧭 Mapped Types

Mapped types transform existing types.

```ts
interface User {
  id: number;
  name: string;
  email: string;
}

type OptionalUser = {
  [K in keyof User]?: User[K];
};
```

Equivalent to:

```ts
// Partial<User>
```

You can use modifiers:

* `readonly` → Make fields immutable
* `?` → Make optional

```ts
type ReadonlyUser = {
  readonly [K in keyof User]: User[K];
};
```

---

## 🧮 Conditional Types

Conditional types allow type logic using `extends`.

```ts
type IsString<T> = T extends string ? true : false;

let a: IsString<string>; // true
let b: IsString<number>; // false
```

Real-world example:

```ts
type Response<T> = T extends Error ? { success: false } : { success: true; data: T };
```

---

## 🧵 Template Literal Types

Combine literal types dynamically using template strings.

```ts
type Direction = "up" | "down";
type Action = `${Direction}_move`;

let a: Action = "up_move"; // ✅
// let b: Action = "left_move"; ❌ Error
```

You can even compose reusable string unions.

```ts
type EventName<T extends string> = `${T}_event`;
type UserEvents = EventName<"click" | "hover">; // "click_event" | "hover_event"
```

---

## 🔍 Indexed Access Types

Used to extract a specific type from another type.

```ts
interface Product {
  id: number;
  name: string;
  price: number;
}

type ProductIdType = Product["id"]; // number
```

You can also use `keyof` for all keys:

```ts
type Keys = keyof Product; // "id" | "name" | "price"
```

---

## 🔑 Key Remapping in Mapped Types

You can rename keys in mapped types using `as`.

```ts
type PrefixKeys<T> = {
  [K in keyof T as `prefix_${string & K}`]: T[K];
};

type Original = { name: string };
type Prefixed = PrefixKeys<Original>; // { prefix_name: string }
```

---

## 🧠 Infer Keyword

`infer` extracts a type inside a conditional type.

```ts
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

function getUser() {
  return { id: 1, name: "Akash" };
}

type UserType = ReturnType<typeof getUser>; // { id: number; name: string }
```

Another example:

```ts
type PromiseType<T> = T extends Promise<infer U> ? U : never;

type Result = PromiseType<Promise<string>>; // string
```

---

## ♻️ Recursive Type Definitions

Recursive types refer to themselves, often used for nested structures like trees.

```ts
type Category = {
  name: string;
  subcategories?: Category[];
};

const categories: Category = {
  name: "Programming",
  subcategories: [
    { name: "Frontend", subcategories: [{ name: "React" }] },
    { name: "Backend", subcategories: [{ name: "Node.js" }] }
  ]
};
```

---

## 🧾 Summary

* **Union & Intersection** types allow type combinations.
* **Mapped** and **Conditional** types bring logic into type definitions.
* **Template Literal Types** generate string-based types.
* **Infer** extracts inner types dynamically.
* **Recursive** types model hierarchical structures.

---

Next: [Part V – Modules & Namespaces →](#)
