# JavaScript Functions — Complete Reference with Examples

Functions are one of the **core building blocks** of JavaScript. They allow you to encapsulate reusable logic and organize code efficiently.

---

## 🧩 Table of Contents

1. [What is a Function?](#what-is-a-function)
2. [Declaring Functions](#declaring-functions)
3. [Function Expressions](#function-expressions)
4. [Arrow Functions](#arrow-functions)
5. [Parameters and Arguments](#parameters-and-arguments)
6. [Default Parameters](#default-parameters)
7. [Rest Parameters (`...args`)](#rest-parameters-args)
8. [Return Statement](#return-statement)
9. [Function Scope](#function-scope)
10. [Closures](#closures)
11. [Immediately Invoked Function Expression (IIFE)](#immediately-invoked-function-expression-iife)
12. [Callbacks](#callbacks)
13. [Higher-Order Functions](#higher-order-functions)
14. [Function Hoisting](#function-hoisting)
15. [Pure vs Impure Functions](#pure-vs-impure-functions)
16. [Anonymous Functions](#anonymous-functions)
17. [Constructor Functions](#constructor-functions)
18. [Arrow vs Regular Functions](#arrow-vs-regular-functions)
19. [Function Binding (`call`, `apply`, `bind`)](#function-binding-call-apply-bind)
20. [Summary Table](#summary-table)

---

## What is a Function?

A function is a block of code designed to perform a particular task. It can be **defined once** and **executed multiple times**.

```js
function greet() {
  console.log('Hello, World!');
}

greet(); // Hello, World!
```

---

## Declaring Functions

### Function Declaration

```js
function add(a, b) {
  return a + b;
}
console.log(add(2, 3)); // 5
```

---

## Function Expressions

Functions can be stored in variables.

```js
const multiply = function(x, y) {
  return x * y;
};
console.log(multiply(4, 5)); // 20
```

> Unlike declarations, function expressions are **not hoisted**.

---

## Arrow Functions

Compact syntax introduced in **ES6**.

```js
const divide = (a, b) => a / b;
console.log(divide(10, 2)); // 5
```

### Multi-line Arrow Function

```js
const greet = (name) => {
  const msg = `Hello, ${name}!`;
  return msg;
};
```

> Arrow functions do **not have their own `this`**.

---

## Parameters and Arguments

```js
function intro(name, age) {
  console.log(`Name: ${name}, Age: ${age}`);
}
intro('Alice', 25);
```

---

## Default Parameters

Provide fallback values when arguments are missing.

```js
function greet(name = 'Guest') {
  console.log(`Hello, ${name}!`);
}
greet(); // Hello, Guest!
```

---

## Rest Parameters (`...args`)

Collects multiple arguments into an array.

```js
function sum(...nums) {
  return nums.reduce((a, b) => a + b, 0);
}
console.log(sum(1, 2, 3, 4)); // 10
```

---

## Return Statement

Returns values from a function.

```js
function getArea(radius) {
  return Math.PI * radius ** 2;
}
console.log(getArea(5));
```

---

## Function Scope

Functions create their own scope.

```js
let message = 'Global';
function showMessage() {
  let message = 'Local';
  console.log(message);
}
showMessage(); // Local
console.log(message); // Global
```

---

## Closures

Functions remember variables from their outer scope.

```js
function outer() {
  let counter = 0;
  return function inner() {
    counter++;
    console.log(counter);
  };
}
const count = outer();
count(); // 1
count(); // 2
```

> Closure = function + its lexical environment.

---

## Immediately Invoked Function Expression (IIFE)

Executes immediately after creation.

```js
(function() {
  console.log('IIFE runs immediately');
})();
```

---

## Callbacks

Functions passed as arguments.

```js
function fetchData(callback) {
  console.log('Fetching data...');
  callback();
}
fetchData(() => console.log('Data loaded!'));
```

---

## Higher-Order Functions

Functions that **take or return functions**.

```js
function applyOperation(a, b, operation) {
  return operation(a, b);
}
console.log(applyOperation(5, 3, (x, y) => x * y)); // 15
```

---

## Function Hoisting

Function declarations are hoisted — they can be called before being defined.

```js
greet(); // Works
function greet() {
  console.log('Hello');
}
```

Function expressions are **not hoisted**:

```js
sayHi(); // ❌ Error
const sayHi = function() { console.log('Hi'); };
```

---

## Pure vs Impure Functions

### Pure

```js
function add(a, b) {
  return a + b;
}
```

* No side effects
* Deterministic (same input → same output)

### Impure

```js
let count = 0;
function increment() {
  count++;
}
```

* Modifies outer state → side effect

---

## Anonymous Functions

Functions without a name.

```js
setTimeout(function() {
  console.log('Hello after 2 seconds');
}, 2000);
```

---

## Constructor Functions

Used to create multiple objects with similar structure.

```js
function Person(name, age) {
  this.name = name;
  this.age = age;
}
const user = new Person('Bob', 30);
console.log(user.name); // Bob
```

---

## Arrow vs Regular Functions

| Feature             | Regular | Arrow                     |
| ------------------- | ------- | ------------------------- |
| `this` binding      | Dynamic | Lexical (inherits parent) |
| `arguments` object  | Yes     | No                        |
| Used as constructor | Yes     | No                        |
| Syntax              | Verbose | Concise                   |

---

## Function Binding (`call`, `apply`, `bind`)

### `call()`

Invokes immediately with custom `this`.

```js
function sayHello() {
  console.log(`Hello ${this.name}`);
}
const user = { name: 'John' };
sayHello.call(user); // Hello John
```

### `apply()`

Like `call()`, but arguments are in an array.

```js
function introduce(city, country) {
  console.log(`${this.name} from ${city}, ${country}`);
}
introduce.apply(user, ['Paris', 'France']);
```

### `bind()`

Returns a new function with fixed `this`.

```js
const greetUser = sayHello.bind(user);
greetUser(); // Hello John
```

---

## Summary Table

| Concept        | Example                          | Description           |
| -------------- | -------------------------------- | --------------------- |
| Declaration    | `function add(a,b){}`            | Hoisted, reusable     |
| Expression     | `const fn = function(){}`        | Not hoisted           |
| Arrow          | `const fn = ()=>{}`              | No `this`, concise    |
| Default Params | `function fn(a=1){}`             | Fallback values       |
| Rest Params    | `(...args)`                      | Collect arguments     |
| Closure        | `function outer(){return inner}` | Remembers scope       |
| IIFE           | `(function(){})()`               | Executes instantly    |
| Callback       | `setTimeout(()=>{},1000)`        | Passed function       |
| HOF            | `map, filter, reduce`            | Take/return functions |
| Binding        | `.call(), .apply(), .bind()`     | Control `this`        |

---

**Next Suggested Topic:** 🧠 *JavaScript Scope and Hoisting — Complete Reference with Examples*
