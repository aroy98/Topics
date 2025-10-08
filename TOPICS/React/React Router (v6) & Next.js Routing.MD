# 🧭 React Mastery Series – Part IV: React Router (v6) & Next.js Routing

---

## 📘 Table of Contents

1. [Overview](#overview)
2. [When to use React Router vs Next.js Routing](#when-to-use-react-router-vs-nextjs-routing)
3. [React Router v6 – Setup & Basics](#react-router-v6--setup--basics)
4. [React Router v6 – Nested Routes & Layouts](#react-router-v6--nested-routes--layouts)
5. [React Router v6 – Route Params, Loaders, and Actions](#react-router-v6--route-params-loaders-and-actions)
6. [React Router v6 – Navigation, Redirects & Guards](#react-router-v6--navigation-redirects--guards)
7. [Next.js Routing – File-system Routing Basics](#nextjs-routing--file-system-routing-basics)
8. [Next.js Routing – Dynamic Routes & Catch-all Routes](#nextjs-routing--dynamic-routes--catch-all-routes)
9. [Next.js – Nested Layouts (App Router)](#nextjs--nested-layouts-app-router)
10. [Next.js – Data Fetching Strategies (Pages & App Router)](#nextjs--data-fetching-strategies-pages--app-router)
11. [Client-side Navigation & Linking (Both)](#client-side-navigation--linking-both)
12. [Authentication & Route Protection (Both)](#authentication--route-protection-both)
13. [SEO, SSG, SSR Considerations](#seo-ssg-ssr-considerations)
14. [Performance & Bundle Splitting](#performance--bundle-splitting)
15. [Migration Tips (React Router → Next.js)](#migration-tips-react-router--nextjs)
16. [Best Practices Summary](#best-practices-summary)

---

## 🧭 Overview

This part covers **React Router v6** (client-side SPA routing) and **Next.js** file-system routing (both Pages and App Router). You’ll get practical setup, examples, patterns for nested layouts, data loading, navigation, route protection, SEO implications, and when to choose which routing model.

---

## ⚖️ When to use React Router vs Next.js Routing

* **React Router v6**: Best for client-side SPAs where you control the entire routing behavior and want flexible nested routes, programmatic navigation, and fine-grained control over transitions.
* **Next.js Routing**: Best for full-stack React apps with server-side rendering, static site generation, hybrid rendering, and built-in optimizations. Use the Pages Router for stability and App Router for modern nested layouts and server components.

Use **React Router** when you need an SPA embedded in a bigger non-Next app. Use **Next.js** for websites and apps where SEO, performance, and edge rendering matter.

---

## ⚛️ React Router v6 – Setup & Basics

### Installation

```bash
npm install react-router-dom@6
```

### Basic Usage

```tsx
// index.tsx
import { BrowserRouter } from 'react-router-dom';
import App from './App';

ReactDOM.createRoot(document.getElementById('root')!).render(
  <BrowserRouter>
    <App />
  </BrowserRouter>
);

// App.tsx
import { Routes, Route, Link } from 'react-router-dom';
import Home from './pages/Home';
import About from './pages/About';

export default function App() {
  return (
    <div>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
      </nav>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </div>
  );
}
```

---

## 🧩 React Router v6 – Nested Routes & Layouts

Nested routes let you compose UI and layouts.

```tsx
// App.tsx
<Routes>
  <Route path="/" element={<MainLayout />}>
    <Route index element={<Home />} />
    <Route path="dashboard" element={<Dashboard />}>
      <Route index element={<Overview />} />
      <Route path="reports" element={<Reports />} />
    </Route>
  </Route>
</Routes>

// MainLayout.tsx
import { Outlet } from 'react-router-dom';
export default function MainLayout() {
  return (
    <div>
      <Header />
      <Outlet /> {/* renders child routes */}
      <Footer />
    </div>
  );
}
```

Use `Outlet` to render nested child routes inside a parent layout.

---

## 📦 React Router v6 – Route Params, Loaders, and Actions

### Route Params

```tsx
// route: /users/:userId
import { useParams } from 'react-router-dom';
function UserProfile() {
  const { userId } = useParams();
  // fetch user by userId
}
```

### Data Loaders & Actions (Remix-style features in RR6+)

`react-router` supports loaders/actions for data on routes (especially in RR6.4+).

```tsx
// route config using createBrowserRouter
import { createBrowserRouter } from 'react-router-dom';

const router = createBrowserRouter([
  {
    path: '/users/:userId',
    element: <UserProfile />,
    loader: async ({ params }) => fetch(`/api/users/${params.userId}`).then(r=>r.json())
  }
]);
```

In component, use `useLoaderData()` to read loader results.

---

## 🛡️ React Router v6 – Navigation, Redirects & Guards

### Programmatic Navigation

```tsx
import { useNavigate } from 'react-router-dom';
function Login() {
  const navigate = useNavigate();
  function onLogin() {
    // after login
    navigate('/dashboard');
  }
}
```

### Redirects (Navigate)

```tsx
import { Navigate } from 'react-router-dom';
// inside element
return <Navigate to="/login" replace />;
```

### Route Guards

Create wrapper components that check auth before rendering child routes.

```tsx
function RequireAuth({ children }: { children: JSX.Element }) {
  const isAuth = useAuth();
  return isAuth ? children : <Navigate to="/login" replace />;
}

// usage
<Route path="/dashboard" element={<RequireAuth><Dashboard /></RequireAuth>} />
```

---

## 📁 Next.js Routing – File-system Routing Basics (Pages Router)

### File-based routes

Create files under `pages/` and Next.js maps them to routes.

```
/pages/index.tsx      -> /
/pages/about.tsx      -> /about
/pages/blog/index.tsx -> /blog
/pages/blog/[id].tsx  -> /blog/:id
```

### Link component

```tsx
import Link from 'next/link';
<Link href="/about">About</Link>
```

---

## 🧭 Next.js Routing – Dynamic Routes & Catch-all Routes

### Dynamic Routes

Create `pages/posts/[slug].tsx` to match `/posts/my-article`.

### Catch-all

`pages/docs/[...slug].tsx` matches `/docs/a/b/c` with `slug` as array.

### Optional Catch-all

`pages/docs/[[...slug]].tsx` allows `/docs` and `/docs/a/b`.

---

## 🏗️ Next.js – Nested Layouts (App Router)

App Router (from Next.js 13+) uses `app/` directory with nested layouts and server components.

```
/app/layout.tsx        -> root layout
/app/dashboard/layout.tsx -> dashboard layout
/app/dashboard/page.tsx   -> dashboard index page
/app/dashboard/settings/page.tsx -> nested page
```

Layouts persist UI between navigations and enable partial rendering.

### Example layout

```tsx
// app/layout.tsx
export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html>
      <body>
        <Header />
        {children}
        <Footer />
      </body>
    </html>
  );
}
```

---

## ⚡ Next.js – Data Fetching Strategies (Pages & App Router)

### Pages Router

* `getStaticProps` – SSG at build time
* `getStaticPaths` – dynamic routes + SSG
* `getServerSideProps` – SSR on each request

### App Router

* Server Components fetch data directly on the server (async components)
* `fetch` with `{ next: { revalidate: n } }` for ISR-like caching
* `use` + Suspense (experimental patterns)

Example (Pages):

```tsx
export async function getStaticProps() {
  const res = await fetch('https://api.example.com/posts');
  const posts = await res.json();
  return { props: { posts } };
}
```

Example (App Router server component):

```tsx
export default async function Page() {
  const res = await fetch('https://api.example.com/posts');
  const posts = await res.json();
  return <PostList posts={posts} />;
}
```

---

## 🔗 Client-side Navigation & Linking (Both)

* **React Router:** `<Link to="/path">` and `useNavigate()` for programmatic navigation.
* **Next.js:** `<Link href="/path">` and `useRouter().push('/path')`.

Prefetching:

* Next.js automatically prefetches linked pages in production.
* React Router can lazy-load route modules via dynamic `import()` and `React.lazy`.

---

## 🔐 Authentication & Route Protection (Both)

### Client-side guard (React Router)

Use `RequireAuth` wrapper as shown earlier.

### Next.js – Protecting Pages

* **Pages Router:** Use `getServerSideProps` to check auth on server and redirect before render.

```tsx
export async function getServerSideProps(context) {
  const token = getTokenFromCookies(context.req);
  if (!token) return { redirect: { destination: '/login', permanent: false } };
  return { props: {} };
}
```

* **App Router:** Use middleware (`middleware.ts`) for cross-cutting checks, or server components to verify session.

### Middleware example

```ts
// middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(req: NextRequest) {
  const token = req.cookies.get('token');
  if (!token) return NextResponse.redirect(new URL('/login', req.url));
  return NextResponse.next();
}
```

---

## 🔎 SEO, SSG, SSR Considerations

* **React Router** apps are client-side; they need SSR solutions (Next.js, Remix) or prerendering for SEO-sensitive pages.
* **Next.js** provides built-in SSR/SSG/ISR for SEO and fast initial loads.
* Use meta tags and structured data with `next/head` or `react-helmet` in React Router apps.

---

## 🚀 Performance & Bundle Splitting

* **Code-splitting**: React Router supports lazy-loaded routes with `React.lazy` and `Suspense`.
* **Next.js** automatically splits pages and prefetches linked pages.
* Prefer route-level splitting for large modules.

React Router example:

```tsx
const Dashboard = React.lazy(() => import('./pages/Dashboard'));
// in route
<Route path="/dashboard" element={<React.Suspense fallback={<Loading/>}><Dashboard/></React.Suspense>} />
```

---

## 🔁 Migration Tips (React Router → Next.js)

* Map route paths to `pages/` or `app/` file structure.
* Move data loading into `getStaticProps`/`getServerSideProps` or server components.
* Replace `BrowserRouter` client-only logic with Next.js routing.
* Convert layouts into `app/layout.tsx` or custom `_app.tsx`.
* Rework dynamic param usage from `useParams()` to `useRouter().query` or async component params in App Router.

---

## ✅ Best Practices Summary

* Choose Next.js when SEO, SSR, SSG, and edge caching matter.
* Choose React Router for standalone SPAs with advanced client routing needs.
* Prefer nested layouts for composability (React Router `Outlet` / Next.js `layout.tsx`).
* Use server-side checks for auth when possible (Next.js middleware or `getServerSideProps`).
* Lazy-load heavy routes and components to reduce bundle size.
* Keep routes small and focused; colocate data fetching with page components.

---

## 🧾 Summary

This guide covered routing for both React Router v6 and Next.js (Pages & App Router). You now have practical patterns for nested routing, data loading, auth, SEO, and migration strategies between the two systems.

---

Next: [Part V – Advanced Patterns →](#)
