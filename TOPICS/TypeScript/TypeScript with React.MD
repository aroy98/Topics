# ⚛️ TypeScript Mastery Series – Part VII: TypeScript with React

---

## 📘 Table of Contents

1. [Introduction](#introduction)
2. [Setting Up React with TypeScript](#setting-up-react-with-typescript)
3. [Typing Functional Components](#typing-functional-components)
4. [Typing Props and Children](#typing-props-and-children)
5. [Typing State and Events](#typing-state-and-events)
6. [Typing Hooks (useState, useEffect, useRef, useReducer)](#typing-hooks-usestate-useeffect-useref-usereducer)
7. [Context API with TypeScript](#context-api-with-typescript)
8. [Typing Custom Hooks](#typing-custom-hooks)
9. [Refs and Forwarded Refs](#refs-and-forwarded-refs)
10. [Form Handling and Controlled Inputs](#form-handling-and-controlled-inputs)
11. [Advanced Component Patterns](#advanced-component-patterns)
12. [Summary](#summary)

---

## 🧠 Introduction

TypeScript makes React development safer and more predictable by providing **type safety for props, state, events, and hooks**. It enhances IntelliSense, reduces runtime bugs, and improves large-scale app maintainability.

---

## ⚙️ Setting Up React with TypeScript

### Using Create React App

```bash
npx create-react-app my-app --template typescript
```

### Using Vite (Recommended)

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

### Folder Structure

```
my-app/
├── src/
│   ├── components/
│   ├── App.tsx
│   └── main.tsx
└── tsconfig.json
```

---

## 🧩 Typing Functional Components

```tsx
import React from 'react';

interface GreetingProps {
  name: string;
}

const Greeting: React.FC<GreetingProps> = ({ name }) => {
  return <h1>Hello, {name}!</h1>;
};

export default Greeting;
```

`React.FC` (FunctionComponent) automatically types `children` and ensures correct JSX typing.

---

## 🧾 Typing Props and Children

### Props Example

```tsx
interface ButtonProps {
  label: string;
  onClick: () => void;
}

const Button: React.FC<ButtonProps> = ({ label, onClick }) => (
  <button onClick={onClick}>{label}</button>
);
```

### Children Example

```tsx
interface CardProps {
  title: string;
  children: React.ReactNode;
}

const Card: React.FC<CardProps> = ({ title, children }) => (
  <div>
    <h2>{title}</h2>
    <div>{children}</div>
  </div>
);
```

---

## ⚡ Typing State and Events

### useState

```tsx
const [count, setCount] = React.useState<number>(0);
```

### Event Typing

```tsx
const handleInputChange = (e: React.ChangeEvent<HTMLInputElement>) => {
  console.log(e.target.value);
};

const handleClick = (e: React.MouseEvent<HTMLButtonElement>) => {
  console.log('Button clicked');
};
```

---

## 🔄 Typing Hooks (useState, useEffect, useRef, useReducer)

### useEffect

```tsx
React.useEffect(() => {
  document.title = `Count: ${count}`;
}, [count]);
```

### useRef

```tsx
const inputRef = React.useRef<HTMLInputElement>(null);

const focusInput = () => {
  inputRef.current?.focus();
};
```

### useReducer

```tsx
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

const [state, dispatch] = React.useReducer(reducer, { count: 0 });
```

---

## 🧠 Context API with TypeScript

```tsx
interface ThemeContextType {
  theme: 'light' | 'dark';
  toggleTheme: () => void;
}

const ThemeContext = React.createContext<ThemeContextType | undefined>(undefined);

const ThemeProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const [theme, setTheme] = React.useState<'light' | 'dark'>('light');

  const toggleTheme = () => setTheme(theme === 'light' ? 'dark' : 'light');

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

export const useTheme = () => {
  const context = React.useContext(ThemeContext);
  if (!context) throw new Error('useTheme must be used within ThemeProvider');
  return context;
};
```

---

## 🧩 Typing Custom Hooks

```tsx
function useFetch<T>(url: string): { data: T | null; loading: boolean; error: string | null } {
  const [data, setData] = React.useState<T | null>(null);
  const [loading, setLoading] = React.useState(true);
  const [error, setError] = React.useState<string | null>(null);

  React.useEffect(() => {
    fetch(url)
      .then((res) => res.json())
      .then(setData)
      .catch((err) => setError(err.message))
      .finally(() => setLoading(false));
  }, [url]);

  return { data, loading, error };
}

// Usage
const { data, loading, error } = useFetch<{ id: number; title: string }>('https://api.example.com/posts');
```

---

## 🔗 Refs and Forwarded Refs

```tsx
interface InputProps {
  label: string;
}

const CustomInput = React.forwardRef<HTMLInputElement, InputProps>(({ label }, ref) => (
  <label>
    {label}
    <input ref={ref} />
  </label>
));

const Parent = () => {
  const ref = React.useRef<HTMLInputElement>(null);
  return <CustomInput ref={ref} label="Enter name:" />;
};
```

---

## 📝 Form Handling and Controlled Inputs

```tsx
const Form = () => {
  const [formData, setFormData] = React.useState<{ name: string; email: string }>({
    name: '',
    email: ''
  });

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    setFormData({ ...formData, [e.target.name]: e.target.value });
  };

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    console.log(formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input name="name" value={formData.name} onChange={handleChange} />
      <input name="email" value={formData.email} onChange={handleChange} />
      <button type="submit">Submit</button>
    </form>
  );
};
```

---

## ⚙️ Advanced Component Patterns

### Generic Components

```tsx
interface ListProps<T> {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
}

function List<T>({ items, renderItem }: ListProps<T>) {
  return <ul>{items.map(renderItem)}</ul>;
}

const App = () => (
  <List items={["Apple", "Banana"]} renderItem={(item) => <li key={item}>{item}</li>} />
);
```

### Higher-Order Components (HOC)

```tsx
function withLogger<P>(Component: React.ComponentType<P>) {
  return (props: P) => {
    console.log('Props:', props);
    return <Component {...props} />;
  };
}
```

---

## 🧾 Summary

* TypeScript makes React more predictable with typed props, events, and hooks.
* Use `React.FC` for consistent component definitions.
* Strongly type custom hooks and context providers.
* Use generics for reusable components.
* Leverage TypeScript’s type inference with hooks and events.

---

Next: [Part VIII – Tooling & Configuration →](#)
