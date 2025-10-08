# JavaScript Objects — Complete Reference with Examples

> Objects are one of the most fundamental data structures in JavaScript. They store key–value pairs and power nearly every JS construct (arrays, functions, classes — all objects under the hood).

---

## Table of Contents

1. [Creating Objects](#creating-objects)
2. [Accessing Properties](#accessing-properties)
3. [Adding & Deleting Properties](#adding--deleting-properties)
4. [Checking for Property Existence](#checking-for-property-existence)
5. [Iterating Over Objects](#iterating-over-objects)
6. [Copying Objects (Shallow vs Deep)](#copying-objects-shallow-vs-deep)
7. [Nested Objects](#nested-objects)
8. [Destructuring Objects](#destructuring-objects)
9. [Merging Objects](#merging-objects)
10. [Freezing & Sealing Objects](#freezing--sealing-objects)
11. [Useful Object Methods](#useful-object-methods)
12. [Object Comparison (Deep Equality)](#object-comparison-deep-equality)
13. [Object ↔ JSON](#object--json)
14. [Optional Chaining (`?.`)](#optional-chaining-)
15. [Object Spread & Rest](#object-spread--rest)
16. [Summary Table](#summary-table)

---

## Creating Objects

### Object literal

```js
const person = {
  name: "John",
  age: 30,
  isEmployed: true
};
console.log(person.name); // John
```

### `new Object()`

```js
const car = new Object();
car.brand = "Tesla";
car.model = "Model 3";
console.log(car.brand); // Tesla
```

### Constructor function

```js
function User(name, age) {
  this.name = name;
  this.age = age;
}
const user1 = new User("Alice", 25);
console.log(user1.name); // Alice
```

### `Object.create(proto)`

```js
const animal = { eats: true };
const dog = Object.create(animal);
dog.barks = true;
console.log(dog.eats); // true (inherited)
```

---

## Accessing Properties

### Dot notation

```js
person.name; // John
```

### Bracket notation (dynamic keys)

```js
person['age']; // 30
const key = 'isEmployed';
person[key]; // true
```

---

## Adding & Deleting Properties

```js
person.country = 'USA';
console.log(person.country); // USA

delete person.age;
console.log(person.age); // undefined
```

---

## Checking for Property Existence

### `in` operator

```js
'name' in person; // true
'salary' in person; // false
```

### `hasOwnProperty()`

```js
person.hasOwnProperty('isEmployed'); // true
```

---

## Iterating Over Objects

### `for...in`

```js
for (let key in person) {
  console.log(`${key}: ${person[key]}`);
}
```

### `Object.keys()`, `Object.values()`, `Object.entries()`

```js
Object.keys(person);   // ['name', 'isEmployed', 'country']
Object.values(person); // ['John', true, 'USA']
Object.entries(person); // [['name','John'], ['isEmployed',true], ['country','USA']]
```

---

## Copying Objects (Shallow vs Deep)

### Shallow copy — spread

```js
const newPerson = { ...person };
```

### Shallow copy — `Object.assign()`

```js
const clone = Object.assign({}, person);
```

> **Note:** Both create *shallow* copies. Nested objects remain referenced.

### Deep copy — JSON approach (limitations)

```js
const deep = JSON.parse(JSON.stringify(obj));
```

**Limitations:** loses functions, `undefined`, `Date`, `Map`, `Set`, `RegExp`, and circular refs.

---

## Nested Objects

```js
const user = {
  name: 'Alice',
  address: { city: 'New York', zip: 10001 }
};
console.log(user.address.city); // New York
```

---

## Destructuring Objects

### Basic

```js
const { name, country } = person;
console.log(name, country); // John USA
```

### Nested destructuring

```js
const { address: { city } } = user;
console.log(city); // New York
```

### Default values

```js
const { salary = 50000 } = person;
console.log(salary); // 50000
```

---

## Merging Objects

```js
const obj1 = { a: 1, b: 2 };
const obj2 = { b: 3, c: 4 };
const merged = { ...obj1, ...obj2 };
console.log(merged); // { a:1, b:3, c:4 }
```

Order matters — later spreads overwrite earlier keys.

---

## Freezing & Sealing Objects

### `Object.freeze()` — makes object immutable

```js
const config = { apiKey: '12345' };
Object.freeze(config);
config.apiKey = '54321'; // silently ignored (non-strict) or throws in strict mode
console.log(config.apiKey); // 12345
```

### `Object.seal()` — prevents adding/removing props, allows modification

```js
const settings = { theme: 'dark' };
Object.seal(settings);
settings.theme = 'light'; // allowed
delete settings.theme; // ignored
```

---

## Useful Object Methods

| Method                              | Description                         | Example                         |
| ----------------------------------- | ----------------------------------- | ------------------------------- |
| `Object.keys(obj)`                  | Returns array of keys               | `Object.keys(person)`           |
| `Object.values(obj)`                | Returns array of values             | `Object.values(person)`         |
| `Object.entries(obj)`               | Returns key-value pairs             | `Object.entries(person)`        |
| `Object.assign(target, ...sources)` | Copies properties to target         | `Object.assign({}, obj)`        |
| `Object.freeze(obj)`                | Makes object immutable              | `Object.freeze(obj)`            |
| `Object.seal(obj)`                  | Seals object                        | `Object.seal(obj)`              |
| `Object.create(proto)`              | Creates object with given prototype | `Object.create(null)`           |
| `Object.fromEntries(iterable)`      | Builds an object from entries       | `Object.fromEntries([['a',1]])` |

---

## Object Comparison (Deep Equality)

Objects are compared by *reference*:

```js
const a = { name: 'John' };
const b = { name: 'John' };
console.log(a === b); // false
```

Simple deep-equality (works for many cases):

```js
function isEqual(obj1, obj2) {
  return JSON.stringify(obj1) === JSON.stringify(obj2);
}
```

For robust comparisons use libraries (`lodash.isEqual`, `fast-deep-equal`) that handle ordering, types, and edge-cases.

---

## Object ↔ JSON

```js
const json = JSON.stringify(person);
// "{"name":"John","isEmployed":true,"country":"USA"}"

const parsed = JSON.parse(json);
console.log(parsed.name); // John
```

---

## Optional Chaining (`?.`)

Safely access nested props:

```js
console.log(user.address?.city); // New York
console.log(user.profile?.email); // undefined (no error)
```

---

## Object Spread & Rest

### Spread

```js
const copy = { ...person };
```

### Rest

```js
const { name, ...rest } = person;
console.log(rest); // { isEmployed: true, country: 'USA' }
```

---

## Summary Table

| Concept    | Short Example                       | Notes                                |
| ---------- | ----------------------------------- | ------------------------------------ |
| Create     | `{}` or `new Object()`              | Object literal is preferred          |
| Access     | `obj.key` / `obj["key"]`            | Bracket for dynamic keys             |
| Add/Delete | `obj.key = val`, `delete obj.key`   | ---                                  |
| Iterate    | `for...in`, `Object.keys()`         | `for...in` iterates enumerable props |
| Clone      | `{ ...obj }`, `Object.assign`       | Shallow copy                         |
| Merge      | `{...a, ...b}`                      | Later spreads overwrite              |
| Protect    | `Object.freeze()`, `Object.seal()`  | Freeze = immutable                   |
| JSON       | `JSON.stringify()` / `JSON.parse()` | Easy serialization                   |

---

## Tips & Gotchas

* Objects compare by reference, not by value.
* Spread and `Object.assign` are shallow copies — nested objects remain shared.
* `JSON.stringify`/`parse` is a quick deep-clone but loses special types and functions.
* Prefer object literals and `class`/constructor functions for consistent patterns.
* Use `hasOwnProperty` when distinguishing own properties vs inherited ones.

---

## Examples & Small Recipes

### Convert entries back to object

```js
const entries = [['a', 1], ['b', 2]];
const obj = Object.fromEntries(entries);
// { a:1, b:2 }
```

### Remove a key immutably

```js
const { password, ...safe } = user;
// safe has all user props except password
```

### Merge defaults with user options

```js
const defaults = { theme: 'light', pageSize: 20 };
const opts = { pageSize: 50 };
const final = { ...defaults, ...opts };
// { theme: 'light', pageSize: 50 }
```

---

*End of document.*
