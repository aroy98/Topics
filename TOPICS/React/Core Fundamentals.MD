# ⚛️ React Mastery Series – Part I: Core Fundamentals

---

## 📘 Table of Contents

1. [Introduction to React](#introduction-to-react)
2. [JSX Deep Dive](#jsx-deep-dive)
3. [Functional vs Class Components](#functional-vs-class-components)
4. [Rendering & Reconciliation](#rendering--reconciliation)
5. [Props, State, and Lifecycle](#props-state-and-lifecycle)
6. [Event Handling](#event-handling)
7. [Lists and Keys](#lists-and-keys)
8. [Conditional Rendering](#conditional-rendering)
9. [Fragments and Portals](#fragments-and-portals)
10. [Summary](#summary)

---

## 🧠 Introduction to React

React is a declarative, component-based JavaScript library for building user interfaces. It focuses on composing UI from small, reusable pieces called **components**. React uses a virtual DOM to efficiently update the real DOM.

### Why React?

* Component-driven architecture
* Declarative UI updates
* Strong ecosystem (React Router, Redux, React Query)
* Great developer experience (Fast Refresh, DevTools)

---

## 🧩 JSX Deep Dive

JSX is a syntax extension that looks like HTML but compiles to `React.createElement` calls.

```jsx
const element = <h1 className="title">Hello, React!</h1>;
```

### Under the hood

```js
const element = React.createElement('h1', { className: 'title' }, 'Hello, React!');
```

### Expressions in JSX

```jsx
const name = 'Akash';
const el = <h2>{`Hello, ${name}`}</h2>;
```

### JSX rules

* Use `className` instead of `class`.
* Use `htmlFor` instead of `for`.
* JSX must return a single parent (use fragments `<>...</>` if needed).

---

## ⚙️ Functional vs Class Components

### Functional Component (preferred)

```jsx
function Greeting({ name }) {
  return <h1>Hello, {name}</h1>;
}
```

### Class Component (legacy)

```jsx
class Greeting extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

Functional components with hooks are the modern standard—simpler and less verbose.

---

## 🔁 Rendering & Reconciliation

React updates the virtual DOM and reconciles differences with the real DOM using efficient diffing algorithms.

Key points:

* Re-render occurs on state or prop changes.
* React batches updates for performance.
* Keys help React identify items in lists for minimal updates.

---

## 🧾 Props, State, and Lifecycle

### Props

Props are read-only inputs passed from parent to child components.

```jsx
function Welcome({ greeting }) {
  return <h1>{greeting}</h1>;
}
```

### State (using Hooks)

```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(c => c + 1)}>Increment</button>
    </div>
  );
}
```

### Lifecycle (Class components)

* `componentDidMount`
* `componentDidUpdate`
* `componentWillUnmount`

With hooks, lifecycle is managed through `useEffect`.

```jsx
import { useEffect } from 'react';

useEffect(() => {
  // mount logic
  return () => {
    // cleanup on unmount
  };
}, [/* dependencies */]);
```

---

## 🖱️ Event Handling

Events are handled with camelCase props and receive synthetic events.

```jsx
function Form() {
  function handleSubmit(e) {
    e.preventDefault();
    console.log('Submitted');
  }

  return (
    <form onSubmit={handleSubmit}>
      <button type="submit">Submit</button>
    </form>
  );
}
```

Types of events: mouse, keyboard, form, clipboard, focus, etc.

---

## 📋 Lists and Keys

When rendering lists, provide a stable `key` to each item to help React reconcile.

```jsx
function TodoList({ todos }) {
  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>{todo.text}</li>
      ))}
    </ul>
  );
}
```

Avoid using array index as key when items can reorder.

---

## 🔀 Conditional Rendering

Render components conditionally using logical operators, ternaries, or early returns.

```jsx
{isLoading ? <Spinner /> : <Content />}

{items.length === 0 && <EmptyState />}
```

---

## 🪄 Fragments and Portals

### Fragments

Group children without extra DOM nodes.

```jsx
return (
  <>
    <Header />
    <Main />
  </>
);
```

### Portals

Render a subtree into a DOM node outside the parent hierarchy.

```jsx
ReactDOM.createPortal(<Modal />, document.getElementById('modal-root'));
```

---

## 🧾 Summary

* React builds UIs using components and JSX.
* Functional components + hooks are the modern approach.
* Understand reconciliation, keys, and lifecycle for efficient updates.
* Use fragments and portals for advanced layout control.

---

Next: [Part II – Hooks Mastery →](#)
