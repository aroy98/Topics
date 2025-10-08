# ⚛️ React Mastery Series – Part III: Context, Reducers & State Management

---

## 📘 Table of Contents

1. [Introduction](#introduction)
2. [Why State Management Matters](#why-state-management-matters)
3. [Prop Drilling Problem](#prop-drilling-problem)
4. [React Context API](#react-context-api)
5. [Using useReducer with Context](#using-usereducer-with-context)
6. [Global State Management Example](#global-state-management-example)
7. [Performance Optimization in Context](#performance-optimization-in-context)
8. [Alternative State Management Tools](#alternative-state-management-tools)
9. [Best Practices](#best-practices)
10. [Summary](#summary)

---

## 🧠 Introduction

As React applications grow, managing state across multiple components becomes challenging. The **Context API** and **Reducers** together offer a powerful and scalable solution to manage global or shared state without excessive prop passing.

---

## ⚙️ Why State Management Matters

State defines how your app behaves and what users see.

### Common Problems in Large Apps

* Multiple components need the same data.
* Updating one component requires changes across many.
* Prop drilling (passing props through multiple layers).

React provides several tools for managing state effectively:

* Local State (`useState`)
* Shared State (`Context`)
* Complex State (`useReducer`)

---

## 🪜 Prop Drilling Problem

**Prop drilling** happens when data is passed down through many intermediate components.

```tsx
function App() {
  const [user, setUser] = useState('Akash');
  return <Layout user={user} />;
}

function Layout({ user }: { user: string }) {
  return <Header user={user} />;
}

function Header({ user }: { user: string }) {
  return <p>Welcome, {user}</p>;
}
```

🔴 Issue: If `Header` is deeply nested, every layer must pass `user`, even if they don’t use it.

✅ Solution: Use **React Context**.

---

## 🧩 React Context API

### Creating Context

```tsx
import React, { createContext, useContext, useState } from 'react';

interface UserContextType {
  user: string;
  setUser: (name: string) => void;
}

const UserContext = createContext<UserContextType | undefined>(undefined);

export function UserProvider({ children }: { children: React.ReactNode }) {
  const [user, setUser] = useState('Akash');
  return <UserContext.Provider value={{ user, setUser }}>{children}</UserContext.Provider>;
}

export function useUser() {
  const context = useContext(UserContext);
  if (!context) throw new Error('useUser must be used within a UserProvider');
  return context;
}
```

### Using Context

```tsx
function Header() {
  const { user } = useUser();
  return <h1>Welcome, {user}</h1>;
}

function App() {
  return (
    <UserProvider>
      <Header />
    </UserProvider>
  );
}
```

---

## 🔁 Using useReducer with Context

Combining `useReducer` and `Context` provides a Redux-like pattern for global state.

```tsx
import React, { createContext, useReducer, useContext } from 'react';

interface State {
  count: number;
}
interface Action {
  type: 'increment' | 'decrement';
}

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      return state;
  }
}

const CountContext = createContext<{
  state: State;
  dispatch: React.Dispatch<Action>;
} | null>(null);

export function CountProvider({ children }: { children: React.ReactNode }) {
  const [state, dispatch] = useReducer(reducer, { count: 0 });
  return <CountContext.Provider value={{ state, dispatch }}>{children}</CountContext.Provider>;
}

export function useCount() {
  const context = useContext(CountContext);
  if (!context) throw new Error('useCount must be used within CountProvider');
  return context;
}
```

### Usage

```tsx
function Counter() {
  const { state, dispatch } = useCount();
  return (
    <>
      <p>{state.count}</p>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
    </>
  );
}
```

---

## 🌍 Global State Management Example

### Step 1 – Create Store

```tsx
interface AppState {
  theme: 'light' | 'dark';
}

const initialState: AppState = { theme: 'light' };

function appReducer(state: AppState, action: { type: 'TOGGLE_THEME' }): AppState {
  switch (action.type) {
    case 'TOGGLE_THEME':
      return { theme: state.theme === 'light' ? 'dark' : 'light' };
    default:
      return state;
  }
}
```

### Step 2 – Create Context Provider

```tsx
const AppContext = createContext<{
  state: AppState;
  dispatch: React.Dispatch<{ type: 'TOGGLE_THEME' }>;
} | null>(null);

export function AppProvider({ children }: { children: React.ReactNode }) {
  const [state, dispatch] = useReducer(appReducer, initialState);
  return <AppContext.Provider value={{ state, dispatch }}>{children}</AppContext.Provider>;
}

export function useApp() {
  const context = useContext(AppContext);
  if (!context) throw new Error('useApp must be used within AppProvider');
  return context;
}
```

### Step 3 – Use in Components

```tsx
function ThemeSwitcher() {
  const { state, dispatch } = useApp();
  return (
    <button onClick={() => dispatch({ type: 'TOGGLE_THEME' })}>
      Current Theme: {state.theme}
    </button>
  );
}

function App() {
  return (
    <AppProvider>
      <ThemeSwitcher />
    </AppProvider>
  );
}
```

---

## ⚡ Performance Optimization in Context

### Avoid Unnecessary Re-renders

* Split large contexts into smaller, focused ones.
* Use `React.memo` or `useMemo` for derived data.
* Move heavy computations outside render.

### Example

```tsx
const value = useMemo(() => ({ state, dispatch }), [state]);
```

---

## 🧩 Alternative State Management Tools

| Library           | Description                                   | Ideal Use Case              |
| ----------------- | --------------------------------------------- | --------------------------- |
| **Redux Toolkit** | Predictable state container with immutability | Large enterprise apps       |
| **Zustand**       | Lightweight state manager with hooks          | Small to medium apps        |
| **Recoil**        | Atom-based state model from Meta              | React + concurrent features |
| **Jotai**         | Minimalistic atom-based state                 | Fine-grained reactivity     |
| **MobX**          | Observable-based reactive system              | Complex data models         |

---

## ✅ Best Practices

* Keep Context **lightweight** – avoid storing massive data.
* Use **multiple contexts** for independent features.
* Combine Context + Reducer for predictable flow.
* Memoize context values to reduce re-renders.
* Extract state logic into **custom hooks**.

---

## 🧾 Summary

* Context API replaces prop drilling and simplifies shared state.
* Combining Context + useReducer creates scalable global stores.
* Optimize performance with memoization and smaller contexts.
* Use external libraries (Redux, Zustand, Recoil) for advanced needs.

---

Next: [Part IV – React Router & Navigation →](#)
