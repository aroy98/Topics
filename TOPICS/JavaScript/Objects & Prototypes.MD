# JavaScript Objects & Prototypes — Complete Reference with Examples

JavaScript’s **object and prototype system** forms the foundation of its inheritance model. Understanding how objects and prototypes work together is key to mastering both traditional and modern class-based JavaScript.

---

## 🧠 Table of Contents

1. [What Are Objects?](#what-are-objects)
2. [Creating Objects](#creating-objects)
3. [Object Methods](#object-methods)
4. [Prototypes — The Foundation of Inheritance](#prototypes--the-foundation-of-inheritance)
5. [Prototype Chain](#prototype-chain)
6. [The `__proto__` Property](#__proto__-property)
7. [The `prototype` Property (on Functions)](#the-prototype-property-on-functions)
8. [Prototype Inheritance](#prototype-inheritance)
9. [Object.create() and Custom Prototypes](#objectcreate-and-custom-prototypes)
10. [hasOwnProperty() vs in Operator](#hasownproperty-vs-in-operator)
11. [Extending Built-in Prototypes](#extending-built-in-prototypes)
12. [ES6 Classes and Prototype Integration](#es6-classes-and-prototype-integration)
13. [Prototype Methods Reference](#prototype-methods-reference)
14. [Performance and Memory Considerations](#performance-and-memory-considerations)
15. [Summary Table](#summary-table)

---

## 🔹 What Are Objects?

An **object** is a collection of key–value pairs (properties and methods). In JavaScript, nearly everything (arrays, functions, classes) is built on top of objects.

```js
const user = {
  name: 'Alice',
  age: 25,
  greet() {
    console.log(`Hello, ${this.name}`);
  }
};
user.greet(); // Hello, Alice
```

---

## 🔹 Creating Objects

### 1. Object Literal

```js
const person = { name: 'John', age: 30 };
```

### 2. Using `new Object()`

```js
const car = new Object();
car.brand = 'Tesla';
car.model = 'Model Y';
```

### 3. Constructor Function

```js
function User(name, age) {
  this.name = name;
  this.age = age;
}
const user1 = new User('Bob', 25);
```

### 4. Using `Object.create()`

```js
const proto = { greet() { console.log('Hi!'); } };
const obj = Object.create(proto);
obj.greet(); // Hi!
```

---

## 🔹 Object Methods

| Method                 | Description                         |
| ---------------------- | ----------------------------------- |
| `Object.keys(obj)`     | Returns keys as array               |
| `Object.values(obj)`   | Returns values as array             |
| `Object.entries(obj)`  | Returns key-value pairs             |
| `Object.assign()`      | Copies properties                   |
| `Object.freeze()`      | Makes object immutable              |
| `Object.seal()`        | Prevents adding/removing properties |
| `Object.create(proto)` | Creates object with given prototype |

---

## 🔹 Prototypes — The Foundation of Inheritance

Every JavaScript object has an internal link to another object called its **prototype**.

* This prototype provides shared properties and methods.
* When accessing a property, JS looks up the **prototype chain** until it finds it.

```js
const animal = { eats: true };
const dog = Object.create(animal);
dog.barks = true;

console.log(dog.eats); // true (inherited)
```

---

## 🔹 Prototype Chain

When you access `dog.eats`, JS searches:

1. `dog` → hasOwnProperty? ❌
2. `dog.__proto__` → found `animal.eats`

If not found, it continues up the chain until `Object.prototype`, then returns `undefined`.

### Diagram

```
dog → animal → Object.prototype → null
```

---

## 🔹 `__proto__` Property

Every object (except one) has a `__proto__` property pointing to its prototype.

```js
const obj = {};
console.log(obj.__proto__ === Object.prototype); // true
```

It’s mostly for inspection — not recommended for setting. Use `Object.getPrototypeOf()` or `Object.setPrototypeOf()`.

```js
Object.getPrototypeOf(obj);
Object.setPrototypeOf(obj, anotherObj);
```

---

## 🔹 The `prototype` Property (on Functions)

Functions (used as constructors) have a special `.prototype` property — the object that will become the prototype of instances created by `new`.

```js
function Person(name) {
  this.name = name;
}
Person.prototype.greet = function() {
  console.log('Hi ' + this.name);
};

const p1 = new Person('Alice');
p1.greet(); // Hi Alice
```

### How It Works

1. When `new Person()` is called → creates an empty object.
2. Sets that object’s internal `[[Prototype]]` = `Person.prototype`.
3. Executes `Person` constructor.

---

## 🔹 Prototype Inheritance

Objects can inherit properties and methods through their prototype chain.

```js
function Animal(name) {
  this.name = name;
}
Animal.prototype.speak = function() {
  console.log(`${this.name} makes a sound`);
};

function Dog(name) {
  Animal.call(this, name); // call parent constructor
}
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;

Dog.prototype.speak = function() {
  console.log(`${this.name} barks`);
};

const dog = new Dog('Buddy');
dog.speak(); // Buddy barks
```

---

## 🔹 Object.create() and Custom Prototypes

`Object.create(proto)` allows you to create an object with a specific prototype.

```js
const animal = {
  speak() { console.log('Animal sound'); }
};
const cat = Object.create(animal);
cat.speak(); // Animal sound
```

---

## 🔹 hasOwnProperty() vs in Operator

```js
const obj = { a: 1 };
console.log('a' in obj); // true
console.log('toString' in obj); // true (inherited)

console.log(obj.hasOwnProperty('a')); // true
console.log(obj.hasOwnProperty('toString')); // false
```

✅ `in` → checks own + inherited properties.
✅ `hasOwnProperty()` → checks only own properties.

---

## 🔹 Extending Built-in Prototypes

You can add methods to built-ins like `Array`, `String`, etc. (but it’s risky!).

```js
Array.prototype.last = function() {
  return this[this.length - 1];
};
console.log([1, 2, 3].last()); // 3
```

⚠️ Avoid modifying built-in prototypes in production — it can cause conflicts.

---

## 🔹 ES6 Classes and Prototype Integration

Classes in ES6 are syntactic sugar over prototype-based inheritance.

```js
class Animal {
  constructor(name) {
    this.name = name;
  }
  speak() {
    console.log(`${this.name} makes a sound`);
  }
}

class Dog extends Animal {
  speak() {
    console.log(`${this.name} barks`);
  }
}

const dog = new Dog('Rocky');
dog.speak(); // Rocky barks
```

Internally:

* `Dog.prototype` inherits from `Animal.prototype`.
* Instances share methods via the prototype chain.

---

## 🔹 Prototype Methods Reference

| Method                              | Description                         |
| ----------------------------------- | ----------------------------------- |
| `Object.getPrototypeOf(obj)`        | Gets an object’s prototype          |
| `Object.setPrototypeOf(obj, proto)` | Sets prototype                      |
| `obj.isPrototypeOf(otherObj)`       | Checks prototype relationship       |
| `Object.create(proto)`              | Creates object with given prototype |
| `hasOwnProperty(prop)`              | Checks if property is own           |
| `Object.hasOwn(obj, prop)`          | ES2022 safer alternative            |

---

## 🔹 Performance and Memory Considerations

✅ Shared methods via prototypes save memory.
✅ Avoid deep prototype chains (slow lookup).
✅ Don’t modify prototypes dynamically — breaks optimization.
✅ Prefer `class` for readability and maintainability.

---

## 🔹 Summary Table

| Concept         | Description                   | Example                                 |
| --------------- | ----------------------------- | --------------------------------------- |
| `__proto__`     | Object’s internal link        | `obj.__proto__ === Object.prototype`    |
| `.prototype`    | Function’s prototype property | `Function.prototype.method`             |
| Inheritance     | Objects share methods         | `Object.create(proto)`                  |
| hasOwnProperty  | Own vs inherited              | `obj.hasOwnProperty('a')`               |
| Object.create   | Create with custom prototype  | `Object.create(proto)`                  |
| ES6 Class       | Modern inheritance syntax     | `class Dog extends Animal {}`           |
| Prototype Chain | Lookup path                   | `obj → proto → Object.prototype → null` |

---

### ✅ In Short

* Every object has a **prototype**.
* The **prototype chain** enables inheritance.
* `__proto__` points to the prototype.
* Functions have a `.prototype` used during instantiation.
* ES6 `class` is just a cleaner syntax over prototypes.

---

**Next Suggested Topic:** ⚡ *JavaScript Asynchronous Programming — Callbacks, Promises, Async/Await, and Event Loop Integration (Deep Dive)*
