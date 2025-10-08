# 🪝 React Mastery Series – Part II: Hooks Mastery

---

## 📘 Table of Contents

1. [Introduction to Hooks](#introduction-to-hooks)
2. [useState – Managing Component State](#usestate--managing-component-state)
3. [useEffect – Side Effects & Lifecycle](#useeffect--side-effects--lifecycle)
4. [useRef – Persistent Values & DOM References](#useref--persistent-values--dom-references)
5. [useMemo – Performance Optimization](#usememo--performance-optimization)
6. [useCallback – Memoizing Functions](#usecallback--memoizing-functions)
7. [useReducer – Complex State Management](#usereducer--complex-state-management)
8. [Custom Hooks](#custom-hooks)
9. [Common Pitfalls and Best Practices](#common-pitfalls-and-best-practices)
10. [Summary](#summary)

---

## 🧠 Introduction to Hooks

**React Hooks** are functions that let you *use state and lifecycle features* inside functional components — without using classes.

Introduced in **React 16.8**, hooks enable cleaner, more modular, and reusable code.

### 🔑 Key Rules of Hooks

1. **Only call hooks at the top level** – never inside loops, conditions, or nested functions.
2. **Only call hooks from React functions** – custom or functional components.

---

## ⚙️ useState – Managing Component State

The `useState` hook lets you manage local component state.

```tsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

### ✅ Tips

* Initialize with default value (`useState(0)`).
* Use updater function for safe state transitions:

  ```tsx
  setCount(prev => prev + 1);
  ```

---

## 🔄 useEffect – Side Effects & Lifecycle

The `useEffect` hook performs side effects (e.g., API calls, subscriptions, DOM manipulation).

```tsx
import { useEffect, useState } from 'react';

function Users() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    fetch('https://jsonplaceholder.typicode.com/users')
      .then(res => res.json())
      .then(setUsers);
  }, []); // empty array = run once on mount

  return (
    <ul>
      {users.map(u => <li key={u.id}>{u.name}</li>)}
    </ul>
  );
}
```

### 🧩 Cleanup Example

```tsx
useEffect(() => {
  const id = setInterval(() => console.log('tick'), 1000);
  return () => clearInterval(id); // cleanup on unmount
}, []);
```

### ⚡ Common Patterns

* Empty dependency → run once on mount
* [var] dependency → re-run on changes
* Return function → cleanup logic

---

## 🧭 useRef – Persistent Values & DOM References

`useRef` stores a mutable value that doesn’t trigger re-renders.

### DOM Reference

```tsx
import { useRef, useEffect } from 'react';

function FocusInput() {
  const inputRef = useRef<HTMLInputElement>(null);

  useEffect(() => {
    inputRef.current?.focus();
  }, []);

  return <input ref={inputRef} placeholder="Focus me automatically" />;
}
```

### Persisting Values Across Renders

```tsx
const renderCount = useRef(0);
useEffect(() => {
  renderCount.current += 1;
});
```

---

## ⚡ useMemo – Performance Optimization

`useMemo` memoizes a **computed value** to avoid expensive recalculations.

```tsx
import { useMemo, useState } from 'react';

function ExpensiveComponent({ num }: { num: number }) {
  const computed = useMemo(() => {
    console.log('Computing...');
    return num ** 2;
  }, [num]);

  return <p>Result: {computed}</p>;
}
```

### When to Use

* Expensive calculations
* Derived values from props/state

Avoid premature optimization — use only when necessary.

---

## 🧩 useCallback – Memoizing Functions

`useCallback` memoizes a **function** reference to prevent unnecessary re-renders.

```tsx
import { useState, useCallback } from 'react';

function Parent() {
  const [count, setCount] = useState(0);

  const increment = useCallback(() => setCount(c => c + 1), []);

  return <Child onClick={increment} />;
}

function Child({ onClick }: { onClick: () => void }) {
  console.log('Child rendered');
  return <button onClick={onClick}>Click Me</button>;
}
```

`useCallback` prevents re-creating the function every render.

---

## 🔁 useReducer – Complex State Management

`useReducer` is an alternative to `useState` for managing complex logic.

```tsx
interface State {
  count: number;
}
interface Action {
  type: 'increment' | 'decrement';
}

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case 'increment': return { count: state.count + 1 };
    case 'decrement': return { count: state.count - 1 };
    default: return state;
  }
}

function Counter() {
  const [state, dispatch] = React.useReducer(reducer, { count: 0 });
  return (
    <>
      <p>{state.count}</p>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
    </>
  );
}
```

Useful for managing state transitions and scalable reducers (like Redux patterns).

---

## 🧠 Custom Hooks

Encapsulate and reuse logic across components.

```tsx
import { useState, useEffect } from 'react';

function useWindowWidth() {
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    const handleResize = () => setWidth(window.innerWidth);
    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, []);

  return width;
}

function App() {
  const width = useWindowWidth();
  return <p>Window width: {width}px</p>;
}
```

### Custom Hook Naming Rule

Always start custom hook names with `use` (e.g., `useAuth`, `useFetch`).

---

## ⚠️ Common Pitfalls and Best Practices

✅ Always include dependencies in `useEffect` and `useCallback`.
✅ Don’t overuse `useMemo` or `useCallback`.
✅ Never call hooks conditionally or in loops.
✅ Extract complex logic into **custom hooks**.
✅ Combine `useReducer` with context for scalable state.

---

## 🧾 Summary

* Hooks add state and lifecycle logic to functional components.
* `useState`, `useEffect`, and `useRef` are the most common hooks.
* `useMemo` and `useCallback` help optimize performance.
* `useReducer` simplifies complex state logic.
* Custom hooks enable code reuse and better abstraction.

---

Next: [Part III – Context, Reducers, and State Management →](#)
