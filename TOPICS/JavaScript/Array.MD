# JavaScript Arrays — Complete Reference with Examples
### Markdown Format

This markdown file contains **all JavaScript Array methods and loops** with **examples**, **explanations**, and **performance comparisons**. It also includes **common interview questions** to help you master arrays.

---

## 🧩 Array Creation & Type Checking

```js
// Array.from()
const chars = Array.from('hey'); // ['h','e','y']

// Array.of()
const single = Array.of(3);      // [3]

// Array.isArray()
Array.isArray(chars);            // true
```

**Explanation:**

* `Array.from()` builds an array from iterables (like strings or NodeLists).
* `Array.of()` creates an array from arguments.
* `Array.isArray()` checks if a value is truly an array.

---

## 📥 Accessor Methods (Non-Mutating)

```js
const a = [1,2,3,4,5];
a.slice(1,4);       // [2,3,4]
a.concat([6,7]);    // [1,2,3,4,5,6,7]
a.join('-');        // '1-2-3-4-5'
a.indexOf(3);       // 2
a.includes(7);      // false
a.at(-1);           // 5
for (const [i,v] of a.entries()) { console.log(i, v); }
```

**Explanation:** Accessors return new arrays or values without changing the original.

---

## 🔧 Mutator Methods (Modify the Array)

```js
const b = [1,2];
b.push(3);          // [1,2,3]
b.pop();            // [1,2]
b.unshift(0);       // [0,1,2]
b.shift();          // [1,2]
b.splice(1,1,'x');  // [1,'x',2]
b.reverse();        // ['x',1,2]
b.sort();           // [1,2,'x'] (lexicographically)
b.fill(0,1,2);      // [1,0,'x']
b.copyWithin(0,1,2) // copies index 1 value to index 0
```

**Explanation:** Mutators directly change the original array.

---

## 🔁 Iteration / Functional Methods

```js
const nums = [1,2,3,4,5];

// forEach
nums.forEach(n => console.log(n));

// map
const doubled = nums.map(n => n*2); // [2,4,6,8,10]

// filter
const evens = nums.filter(n => n%2===0); // [2,4]

// reduce
const sum = nums.reduce((acc,n)=>acc+n,0); // 15

// find / findIndex
nums.find(n => n>3);     // 4
nums.findIndex(n => n>3); // 3

// every / some
nums.every(n => n>0); // true
nums.some(n => n%2===0); // true

// flat / flatMap
const nested = [1,[2,3],[4,[5]]];
nested.flat(1); // [1,2,3,4,[5]]
[1,2].flatMap(n => [n, n*2]); // [1,2,2,4]
```

**Explanation:** Declarative iteration; `map`, `filter`, and `reduce` are the most common.

---

## 🔍 Searching / Slicing Helpers

```js
const arr = [10,20,30,20];
arr.indexOf(20);      // 1
arr.lastIndexOf(20);  // 3
arr.includes(30);     // true
arr.slice(1,3);       // [20,30]
```

**Explanation:** Used for lookups and partial copies without changing the original.

---

## 🧠 Utility & Lesser-Known Methods

```js
// copyWithin
const u = [1,2,3,4,5];
u.copyWithin(0,3,5); // [4,5,3,4,5]

// Immutable variants (ES2023+)
const sortedCopy = [3,1,2].toSorted();   // [1,2,3]
const reversedCopy = [1,2,3].toReversed(); // [3,2,1]
```

**Explanation:** `copyWithin` mutates, while newer `toSorted()`/`toReversed()` return new arrays.

---

## ⚙️ Array Static Methods

```js
Array.from({0:'a',1:'b',length:2}); // ['a','b']
Array.of(); // []
Array.isArray([]); // true
```

**Explanation:** Static methods operate on the `Array` constructor itself.

---

## 🔂 Loops & Iteration Constructs

```js
const arr2 = ['a','b','c'];

// Classic for
for (let i = 0; i < arr2.length; i++) {
  console.log(i, arr2[i]);
}

// Reverse for
for (let i = arr2.length - 1; i >= 0; i--) {
  console.log(arr2[i]);
}

// for...of
for (const v of arr2) console.log(v);

// for...in (not recommended for arrays)
for (const k in arr2) console.log(k, arr2[k]);

// forEach
arr2.forEach((v,i) => console.log(i,v));
```

**Explanation:** Use `for...of` for array values; `forEach` for side effects; `for` for speed and control.

---

## ⚡ Async Iteration

```js
async function* asyncGen() {
  yield 1;
  await new Promise(r => setTimeout(r, 0));
  yield 2;
}
(async () => {
  for await (const x of asyncGen()) {
    console.log(x); // 1, then 2
  }
})();
```

**Explanation:** `for await...of` is used to iterate over asynchronous data sources.

---

## 🧾 Iterators & Manual Iteration

```js
const itArr = ['x','y','z'];
const it = itArr.entries();
console.log(it.next().value); // [0,'x']
console.log(it.next().value); // [1,'y']
for (const [i,v] of itArr.entries()) console.log(i,v);
```

**Explanation:** Iterators provide fine control over iteration; useful for generators or custom loops.

---

## 💡 Common Patterns

### Sum

```js
const sum = [1,2,3,4].reduce((a,b)=>a+b,0); // 10
```

**Explanation:** Aggregates all elements.

### Unique

```js
const unique = [...new Set([1,2,2,3])]; // [1,2,3]
```

**Explanation:** Removes duplicates using `Set`.

### Chunk

```js
function chunk(arr, size) {
  const out = [];
  for (let i=0; i<arr.length; i+=size) out.push(arr.slice(i, i+size));
  return out;
}
chunk([1,2,3,4,5], 2); // [[1,2],[3,4],[5]]
```

**Explanation:** Divides an array into smaller arrays.

### Immutable Update

```js
const people = [{id:1, name:'A', done:false}, {id:2, name:'B', done:false}];
const updated = people.map(p => p.id===2 ? {...p, done:true} : p);
```

**Explanation:** Replace a specific element without mutating the original array.

### Deep Flatten

```js
const flatAll = [1,[2,[3,[4]]]].flat(Infinity); // [1,2,3,4]
```

**Explanation:** Flattens nested arrays of any depth.

### Index + Value Loop

```js
for (const [i, v] of ['a','b','c'].entries()) console.log(i, v);
```

**Explanation:** Cleanly iterates with both index and value.

---

## ⚙️ Performance Comparison

| Task              | Mutating Approach        | Non-Mutating Approach    | Performance                          |
| ----------------- | ------------------------ | ------------------------ | ------------------------------------ |
| Append items      | `push()`                 | `[...arr, newItem]`      | `push()` faster                      |
| Remove first item | `shift()`                | `arr.slice(1)`           | `slice()` faster for large arrays    |
| Sort              | `sort()`                 | `toSorted()`             | `sort()` mutates; `toSorted()` safer |
| Reverse           | `reverse()`              | `toReversed()`           | `reverse()` slightly faster          |
| Filter / Map      | Mutates manually in loop | Use `filter()` / `map()` | Built-ins optimized internally       |

**Summary:** Use **mutating methods** when performance is critical and you control state, and **non-mutating methods** in functional or immutable codebases.

---

## 💬 Common JavaScript Array Interview Questions

1. **Difference between `forEach` and `map`?**
   → `forEach` executes a callback but returns `undefined`; `map` returns a new transformed array.

2. **How do you remove duplicates from an array?**
   → `const unique = [...new Set(arr)];`

3. **Explain the difference between `slice()` and `splice()`.**
   → `slice` is non-mutating, `splice` modifies the array.

4. **How to flatten a nested array?**
   → `arr.flat(Infinity)`.

5. **When would you use `reduce()`?**
   → When you need to accumulate values (e.g., sum, average, transform objects).

6. **What is the difference between `some()` and `every()`?**
   → `some()` returns true if *any* element passes; `every()` if *all* elements pass.

7. **How does `sort()` work internally?**
   → It converts items to strings by default and sorts lexicographically unless a `compareFn` is provided.

8. **Why is `for...in` discouraged for arrays?**
   → It iterates enumerable properties, not numeric indexes, and can include custom properties.

9. **What is the use of `Array.from()`?**
   → Converts iterable or array-like objects to arrays.

10. **Explain the difference between shallow and deep copy.**
    → Array methods like `slice()` or spread (`[...]`) make shallow copies; nested objects still share references.

---

## 🧭 Choosing the Right Tool

| Need                   | Use                                              |
| ---------------------- | ------------------------------------------------ |
| Transform each item    | `map()`                                          |
| Filter items           | `filter()`                                       |
| Aggregate to one value | `reduce()`                                       |
| Side effects only      | `forEach()`                                      |
| Need early break       | `for` / `for...of`                               |
| Avoid mutation         | `map`, `filter`, `slice`, `concat`, `toSorted()` |
| High performance       | classic `for`                                    |

---

✅ **Next Steps:**

* Practice each method interactively in Node or browser console.
* For ES2023+ methods (`toSorted`, `toReversed`), check browser support.
* Review interview questions before coding assessments.

---

## 🧪 Practice Exercises (Problems + Solutions)

Below are exercise problems grouped by difficulty. Try solving each before peeking at the solution. Solutions are short and focused — copy them into a console to test.

### Beginner

**1) Sum of positive numbers**

* Problem: Write a function `sumPositive(arr)` that returns the sum of positive numbers only.

```js
// Solution
function sumPositive(arr) {
  return arr.filter(n => n > 0).reduce((s, n) => s + n, 0);
}
// Example
sumPositive([-2, 3, 5, -1]); // 8
```

**2) Unique and sorted**

* Problem: Return a sorted array of unique numbers from an input array `uniqSorted(arr)`.

```js
// Solution
function uniqSorted(arr) {
  return [...new Set(arr)].sort((a,b) => a - b);
}
// Example
uniqSorted([3,1,2,3]); // [1,2,3]
```

---

### Intermediate

**3) Chunk an array**

* Problem: Implement `chunk(arr, size)` that divides `arr` into chunks of length `size`.

```js
// Solution
function chunk(arr, size) {
  const out = [];
  for (let i = 0; i < arr.length; i += size) out.push(arr.slice(i, i + size));
  return out;
}
// Example
chunk([1,2,3,4,5], 2); // [[1,2],[3,4],[5]]
```

**4) Stable partition (preserve order)**

* Problem: Split array into two arrays (even and odd) while preserving relative order.

```js
// Solution
function partitionEvenOdd(arr) {
  const evens = arr.filter(x => x % 2 === 0);
  const odds = arr.filter(x => x % 2 !== 0);
  return { evens, odds };
}
// Example
partitionEvenOdd([5,2,3,4,1]); // {evens:[2,4], odds:[5,3,1]}
```

---

### Advanced

**5) Deep flatten without `flat`**

* Problem: Implement `deepFlatten(arr)` that flattens arbitrarily nested arrays.

```js
// Solution (recursive)
function deepFlatten(arr) {
  const res = [];
  for (const item of arr) {
    if (Array.isArray(item)) res.push(...deepFlatten(item));
    else res.push(item);
  }
  return res;
}
// Example
deepFlatten([1,[2,[3,[4]]]]); // [1,2,3,4]
```

**6) First non-repeating element**

* Problem: Find the first non-repeating number in an array. Return `undefined` if none.

```js
// Solution
function firstNonRepeating(arr) {
  const freq = new Map();
  for (const x of arr) freq.set(x, (freq.get(x) || 0) + 1);
  return arr.find(x => freq.get(x) === 1);
}
// Example
firstNonRepeating([2,3,2,4,3,5]); // 4
```

---

### Expert

**7) Sliding window max**

* Problem: Given `nums` and window size `k`, return array of maximums for each window (O(n) expected).

```js
// Solution (deque approach)
function maxSlidingWindow(nums, k) {
  if (k === 0) return [];
  const res = [];
  const deque = []; // store indices, decreasing values
  for (let i = 0; i < nums.length; i++) {
    // remove out-of-window indices
    if (deque.length && deque[0] <= i - k) deque.shift();
    // maintain decreasing order
    while (deque.length && nums[deque[deque.length - 1]] <= nums[i]) deque.pop();
    deque.push(i);
    if (i >= k - 1) res.push(nums[deque[0]]);
  }
  return res;
}
// Example
maxSlidingWindow([1,3,-1,-3,5,3,6,7], 3); // [3,3,5,5,6,7]
```

**8) Immutable update by deep path**

* Problem: Given an object and a path (array of keys) replace the value immutably.

```js
// Solution
function setIn(obj, path, value) {
  if (path.length === 0) return value;
  const [key, ...rest] = path;
  return Array.isArray(obj)
    ? Object.assign([], obj, { [key]: setIn(obj[key], rest, value) })
    : { ...obj, [key]: setIn(obj?.[key] ?? {}, rest, value) };
}
// Example
const o = { a: { b: { c: 1 } } };
const newO = setIn(o, ['a','b','c'], 42);
// newO.a.b.c === 42 and o remains unchanged
```

---

## ✅ How to use these exercises

* Try writing each solution on your own first.
* Run the example in Node or a browser console.
* Time yourself for advanced problems to practice interview conditions.

---

**Author:** ChatGPT (GPT-5)

**License:** Free for educational and personal use.
