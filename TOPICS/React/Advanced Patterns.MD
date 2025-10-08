# 🎛️ React Mastery Series – Part V: Advanced Patterns

---

## 📘 Table of Contents

1. [Introduction](#introduction)
2. [Compound Components](#compound-components)
3. [Render Props](#render-props)
4. [Higher-Order Components (HOC)](#higher-order-components-hoc)
5. [Controlled vs Uncontrolled Components](#controlled-vs-uncontrolled-components)
6. [Portals & Modals](#portals--modals)
7. [Error Boundaries](#error-boundaries)
8. [Suspense & Concurrent Patterns](#suspense--concurrent-patterns)
9. [Server Components & Client Components (Next.js)](#server-components--client-components-nextjs)
10. [Performance Patterns (Memoization, Virtualization)](#performance-patterns-memoization-virtualization)
11. [State Machines & XState](#state-machines--xstate)
12. [Design Systems & Component Libraries](#design-systems--component-libraries)
13. [Styling Strategies (CSS-in-JS, Utility-First)](#styling-strategies-css-in-js-utility-first)
14. [Accessibility Patterns (a11y)](#accessibility-patterns-a11y)
15. [Testing Patterns for Components](#testing-patterns-for-components)
16. [Animation Patterns](#animation-patterns)
17. [Micro-frontends & Module Federation](#micro-frontends--module-federation)
18. [Summary & Further Reading](#summary--further-reading)

---

## 🧠 Introduction

Advanced patterns let you design flexible, reusable, and maintainable UI systems. This part covers composition patterns, error handling, concurrency, state orchestration, styling approaches, accessibility, testing, and architectural patterns used in modern React apps.

---

## 🧩 Compound Components

Compound components let parent components coordinate behavior of multiple child components while keeping a clean API.

### Pattern example: Tabs

```tsx
// Tabs.tsx
import React, { createContext, useContext, useState } from 'react';

const TabsContext = createContext(null as any);

export function Tabs({ children, defaultIndex = 0 }: any) {
  const [index, setIndex] = useState(defaultIndex);
  return (
    <TabsContext.Provider value={{ index, setIndex }}>
      {children}
    </TabsContext.Provider>
  );
}

export function TabList({ children }: any) {
  return <div role="tablist">{children}</div>;
}

export function Tab({ children, idx }: any) {
  const { index, setIndex } = useContext(TabsContext);
  return (
    <button role="tab" aria-selected={index === idx} onClick={() => setIndex(idx)}>
      {children}
    </button>
  );
}

export function TabPanel({ children, idx }: any) {
  const { index } = useContext(TabsContext);
  return index === idx ? <div role="tabpanel">{children}</div> : null;
}
```

Benefits:

* Simple API for consumers
* Keeps internal state centralized
* Easy to extend with new behaviors

---

## 🎭 Render Props

Render props expose internal state to children via a function prop.

```tsx
function MouseTracker({ children }: { children: (pos: { x: number; y: number }) => React.ReactNode }) {
  const [pos, setPos] = React.useState({ x: 0, y: 0 });
  React.useEffect(() => {
    const handler = (e: MouseEvent) => setPos({ x: e.clientX, y: e.clientY });
    window.addEventListener('mousemove', handler);
    return () => window.removeEventListener('mousemove', handler);
  }, []);
  return <>{children(pos)}</>;
}

// Usage
<MouseTracker>{({ x, y }) => <div>Mouse at {x}, {y}</div>}</MouseTracker>
```

Use when consumers need control over rendering while using internal logic.

---

## 🔁 Higher-Order Components (HOC)

HOCs wrap a component to inject behavior or data.

```tsx
function withAuth<P extends object>(Component: React.ComponentType<P>) {
  return function Wrapped(props: P) {
    const isAuth = useAuth();
    if (!isAuth) return <LoginRedirect />;
    return <Component {...props} />;
  };
}
```

HOCs are less common with hooks, but still useful for cross-cutting concerns and library authors.

---

## ⚖️ Controlled vs Uncontrolled Components

* **Controlled**: Parent owns the state and passes value + onChange.
* **Uncontrolled**: Component manages its own state (refs)

Controlled example:

```tsx
function Input({ value, onChange }: { value: string; onChange: (v: string) => void }) {
  return <input value={value} onChange={e => onChange(e.target.value)} />;
}
```

Uncontrolled example:

```tsx
function FileInput() {
  const ref = React.useRef<HTMLInputElement>(null);
  const handle = () => { console.log(ref.current?.files); };
  return <input type="file" ref={ref} onChange={handle} />;
}
```

Choose controlled for predictable state and forms; uncontrolled for simpler, less frequent interactions.

---

## 🪄 Portals & Modals

Portals render content outside the parent DOM node (useful for modals, tooltips).

```tsx
function Modal({ children }: { children: React.ReactNode }) {
  return ReactDOM.createPortal(
    <div className="modal">{children}</div>,
    document.getElementById('modal-root')!
  );
}
```

Also handle focus trapping and accessibility when implementing modals.

---

## 🛡️ Error Boundaries

Error Boundaries catch rendering errors in child components.

```tsx
class ErrorBoundary extends React.Component<{ children: React.ReactNode }, { hasError: boolean }> {
  state = { hasError: false };
  static getDerivedStateFromError() { return { hasError: true }; }
  componentDidCatch(error: Error, info: any) { console.error(error, info); }
  render() { return this.state.hasError ? <Fallback /> : this.props.children; }
}
```

Note: Error boundaries only catch errors in lifecycle/rendering, not in event handlers.

---

## 🧭 Suspense & Concurrent Patterns

`Suspense` lets you show fallbacks while waiting for async data or code-split components.

```tsx
const LazyComponent = React.lazy(() => import('./Heavy'));

<Suspense fallback={<Spinner/>}>
  <LazyComponent />
</Suspense>
```

Concurrent features (like transition APIs) improve perceived performance for UI updates.

```tsx
const [isPending, startTransition] = React.useTransition();
startTransition(() => setFilter(query));
```

Use transitions for non-urgent state updates.

---

## 🧾 Server Components & Client Components (Next.js)

Server Components (in Next.js App Router) run on server and can fetch data directly; Client Components run in browser and support hooks.

* Mark client components with `'use client'` at top.
* Prefer server components for static content and heavy data fetching.

Example client component:

```tsx
'use client';
import { useState } from 'react';
export default function Counter() { const [n,setN]=useState(0); return <button onClick={()=>setN(n+1)}>{n}</button> }
```

---

## ⚡ Performance Patterns (Memoization, Virtualization)

* Use `React.memo`, `useMemo`, and `useCallback` to avoid unnecessary renders.
* Virtualize long lists with `react-window` or `react-virtualized`.

Example: `react-window`

```tsx
import { FixedSizeList as List } from 'react-window';
<List height={500} itemCount={1000} itemSize={35} width={300}>{({index, style}) => <div style={style}>Item {index}</div>}</List>
```

Profile with React Profiler and Chrome DevTools.

---

## 🧭 State Machines & XState

State machines provide predictable state transitions, great for complex UI flows.

Example XState:

```ts
import { createMachine, interpret } from 'xstate';
const toggleMachine = createMachine({
  id: 'toggle',
  initial: 'inactive',
  states: {
    inactive: { on: { TOGGLE: 'active' } },
    active: { on: { TOGGLE: 'inactive' } }
  }
});
const service = interpret(toggleMachine).start();
service.send('TOGGLE');
```

Use `@xstate/react` to connect machines to React components.

---

## 🧩 Design Systems & Component Libraries

Design systems standardize UI components, tokens, and accessibility.

Key aspects:

* Atomic components (Button, Input)
* Theming and tokens (colors, spacing)
* Documentation (Storybook)
* Accessibility compliance
* Distribution (npm packages, monorepos)

Use Storybook for component development and visual testing.

---

## 🎨 Styling Strategies (CSS-in-JS, Utility-First)

Options:

* **CSS Modules** (scoped class names)
* **Styled Components / Emotion** (CSS-in-JS)
* **Tailwind CSS** (utility-first)
* **vanilla-extract** (zero-runtime styles)

Choose based on team preferences: design system integration, runtime performance, and DX.

---

## ♿ Accessibility Patterns (a11y)

* Use semantic HTML elements.
* Manage focus for dialogs and keyboard navigation.
* Provide ARIA attributes when needed (`aria-live`, `role`).
* Test with Axe, Lighthouse, and screen readers.

Example: accessible modal focus management and `aria-modal`.

---

## 🧪 Testing Patterns for Components

* Unit test logic with Jest.
* Use React Testing Library for DOM-focused tests.
* Storybook + Chromatic for visual regression testing.

Example test:

```tsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import Button from './Button';

test('click calls handler', async () => {
  const user = userEvent.setup();
  const handle = jest.fn();
  render(<Button onClick={handle}>Click</Button>);
  await user.click(screen.getByText('Click'));
  expect(handle).toHaveBeenCalled();
});
```

---

## ✨ Animation Patterns

* Use CSS transitions for simple animations.
* Use `framer-motion` for complex, interruptible animations.

Example `framer-motion`:

```tsx
import { motion } from 'framer-motion';
<motion.div initial={{ opacity: 0 }} animate={{ opacity: 1 }} exit={{ opacity: 0 }}>Hello</motion.div>
```

---

## 🧩 Micro-frontends & Module Federation

Micro-frontends split a large app into smaller, independently deployable apps. Webpack Module Federation allows sharing components at runtime.

Considerations:

* Shared dependencies (React version alignment)
* Routing and layout composition
* Cross-app communication

---

## 🧾 Summary & Further Reading

* Use composition (compound components, render props, hooks) over inheritance.
* Prioritize accessibility, performance, and testability.
* Consider state machines for complex flows and XState for orchestration.
* Adopt design systems and Storybook for scalable UI development.

**Further reading & tools:** React docs, Kent C. Dodds articles, XState docs, Storybook, React Aria, Framer Motion, react-window.

---

Next: [Part VI – Server & Performance →](#)
