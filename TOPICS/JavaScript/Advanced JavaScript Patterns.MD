---

## Part II — Advanced JavaScript Patterns

### Functional, Reactive, and Design Patterns (Complete Reference with Examples)

### Preface

Having explored JavaScript internals, performance tuning, and debugging, we now move beyond syntax into the realm of **architecture and pattern‑driven development**. This section provides a deep dive into how to structure, scale, and reason about modern JavaScript applications using **Functional**, **Reactive**, and **Design Patterns**.

---

### Table of Contents

1. [Functional Programming](#functional-programming)
2. [Reactive Programming](#reactive-programming)
3. [Design Patterns](#design-patterns)

   1. [Creational Patterns](#creational-patterns)
   2. [Structural Patterns](#structural-patterns)
   3. [Behavioral Patterns](#behavioral-patterns)
4. [Modern Modular and ES Patterns](#modern-modular-and-es-patterns)
5. [TypeScript Equivalents](#typescript-equivalents)
6. [Real-World Architecture Examples](#real-world-architecture-examples)
7. [Comparison Tables](#comparison-tables)
8. [Cheat Sheet — Best Practices](#cheat-sheet--best-practices)
9. [Where to Go Next](#where-to-go-next)

---

## Functional Programming

Functional programming (FP) focuses on **pure functions**, **immutability**, and **composition**. It treats computation as evaluation of mathematical functions and avoids mutable state.

### Pure Functions

A function is *pure* if:

* It returns the same output for the same input.
* It has no side effects.

```js
function add(a, b) {
  return a + b;
}
```

### Immutability

Values should not be mutated; instead, return new ones.

```js
const arr = [1, 2, 3];
const newArr = [...arr, 4]; // [1,2,3,4]
```

### Higher-Order Functions

Functions that take or return other functions.

```js
function multiplier(factor) {
  return x => x * factor;
}
const double = multiplier(2);
```

### Function Composition

Combine small functions into pipelines.

```js
const compose = (...fns) => x => fns.reduceRight((v, f) => f(v), x);
const inc = x => x + 1;
const double = x => x * 2;
const incThenDouble = compose(double, inc);
```

### Data Flow Diagram

```
input → inc(x) → double(x) → output
```

### TypeScript Example

```ts
const compose = <T>(...fns: Array<(arg: T) => T>) => (x: T): T =>
  fns.reduceRight((v, f) => f(v), x);
```

---

## Reactive Programming

Reactive programming models data as **streams** that emit values over time. Libraries like **RxJS** power reactive code.

### Observables

An observable emits data that observers subscribe to.

```js
import { of } from 'rxjs';
import { map, filter } from 'rxjs/operators';

of(1, 2, 3, 4)
  .pipe(filter(x => x % 2 === 0), map(x => x * 10))
  .subscribe(console.log); // 20, 40
```

### Stream Flow Diagram

```
(1)─(2)─(3)─(4)──▶ filter even ─▶ map×10 ─▶ (20)─(40)──▶
```

### Subjects

Subjects act as both observers and observables.

```js
import { Subject } from 'rxjs';
const subject = new Subject();
subject.subscribe(console.log);
subject.next('A');
subject.next('B');
```

---

## Design Patterns

### Creational Patterns

#### Factory Pattern

Creates objects without specifying the exact class.

```js
function createUser(role) {
  if (role === 'admin') return { role, permissions: ['all'] };
  return { role, permissions: ['read'] };
}
```

**UML Diagram**

```
[Client] → [Factory] → [Product]
```

#### Singleton Pattern

Ensure only one instance exists.

```js
class Config {
  static instance;
  constructor(settings) {
    if (Config.instance) return Config.instance;
    this.settings = settings;
    Config.instance = this;
  }
}
```

### Structural Patterns

#### Module Pattern

Encapsulate logic and expose public API.

```js
const Counter = (function() {
  let count = 0;
  return {
    inc: () => ++count,
    dec: () => --count
  };
})();
```

#### Decorator Pattern

Add behavior dynamically.

```js
function logger(fn) {
  return (...args) => {
    console.log('Calling', fn.name);
    return fn(...args);
  };
}
const sum = (a, b) => a + b;
const loggedSum = logger(sum);
```

### Behavioral Patterns

#### Observer Pattern

Notify subscribers of changes.

```js
class Subject {
  observers = [];
  subscribe(obs) { this.observers.push(obs); }
  notify(data) { this.observers.forEach(o => o(data)); }
}
```

**UML Diagram**

```
[Subject] → notify() → [Observer1, Observer2]
```

#### Strategy Pattern

Swap algorithms dynamically.

```js
class Payment {
  setStrategy(strategy) { this.strategy = strategy; }
  pay(amount) { this.strategy.pay(amount); }
}
class CardPayment { pay(a) { console.log('Card', a); } }
class CashPayment { pay(a) { console.log('Cash', a); } }
```

---

## Modern Modular and ES Patterns

### Revealing Module Pattern

Expose only selected members.

```js
const userModule = (() => {
  let name = 'John';
  const getName = () => name;
  return { getName };
})();
```

### ES Modules

```js
// math.js
export const add = (a,b)=>a+b;
// main.js
import { add } from './math.js';
```

### Dependency Injection

```ts
class Service { log() { console.log('service'); } }
class Consumer {
  constructor(private s: Service) {}
  run() { this.s.log(); }
}
```

---

## TypeScript Equivalents

TypeScript enhances design patterns with interfaces and generics.

```ts
interface Strategy { pay(amount: number): void; }
class CardPayment implements Strategy { pay(a:number){console.log('Card',a)} }
class CashPayment implements Strategy { pay(a:number){console.log('Cash',a)} }
```

---

## Real-World Architecture Examples

### React

Use hooks + composition = functional pattern.

```js
function useCounter(initial=0){
  const [count,setCount]=useState(initial);
  const inc=()=>setCount(c=>c+1);
  return {count,inc};
}
```

### Node.js

Use factory pattern for service creation.

```js
function createDbClient(env){
  return env==='prod'?new PgClient():new SqliteClient();
}
```

### Angular

Dependency injection & observer pattern via RxJS subjects.

```ts
@Injectable({providedIn:'root'})
export class DataService{
  private subject=new Subject<string>();
  data$=this.subject.asObservable();
  push(v:string){this.subject.next(v)}
}
```

---

## Comparison Tables

| Category   | Key Concept               | Example             |
| ---------- | ------------------------- | ------------------- |
| Functional | Composition, Immutability | `map/filter/reduce` |
| Reactive   | Streams, Observables      | `RxJS`              |
| Creational | Object Creation           | Factory, Singleton  |
| Structural | Composition               | Module, Decorator   |
| Behavioral | Interaction               | Observer, Strategy  |

---

## Cheat Sheet — Best Practices

* Use **pure functions** for predictable state.
* Compose logic using **map/filter/reduce**.
* Favor **Observables** for async streams.
* Apply **factory** or **strategy** for scalable architectures.
* Encapsulate logic with **modules**.
* Use **TypeScript interfaces** for contracts.

---

## Where to Go Next

Continue exploring:

* **Advanced TypeScript Patterns** (Generics, Mixins, Decorators)
* **Clean Architecture** and SOLID Principles
* **Reactive UI frameworks** (React, Angular, Vue)
* **Microservices and Event-Driven Node.js systems**

---

*End of Part II*
