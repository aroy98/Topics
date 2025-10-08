# ⚙️ React Mastery Series – Part VI: Server & Performance

---

## 📘 Table of Contents

1. [Introduction](#introduction)
2. [React Server Components (RSC)](#react-server-components-rsc)
3. [Server-Side Rendering (SSR)](#server-side-rendering-ssr)
4. [Static Site Generation (SSG)](#static-site-generation-ssg)
5. [Incremental Static Regeneration (ISR)](#incremental-static-regeneration-isr)
6. [Hybrid Rendering Strategies](#hybrid-rendering-strategies)
7. [Edge Rendering & Streaming](#edge-rendering--streaming)
8. [Caching Strategies](#caching-strategies)
9. [Performance Optimization Techniques](#performance-optimization-techniques)
10. [Image Optimization](#image-optimization)
11. [Code Splitting & Lazy Loading](#code-splitting--lazy-loading)
12. [Bundle Analysis & Tree Shaking](#bundle-analysis--tree-shaking)
13. [Memory & Render Performance Debugging](#memory--render-performance-debugging)
14. [Scalability & Deployment Best Practices](#scalability--deployment-best-practices)
15. [Summary](#summary)

---

## 🧠 Introduction

Modern React applications can render on both the **client** and **server**, balancing **performance**, **SEO**, and **developer experience**. This part explores **Server Components**, **SSR**, **SSG**, **ISR**, **Edge Rendering**, and practical optimizations for production-grade React apps.

---

## ⚛️ React Server Components (RSC)

React Server Components allow parts of the UI to render **on the server**, reducing client JavaScript payload.

* Introduced with **Next.js 13+ (App Router)**.
* RSCs don’t include client-side JavaScript.
* They can **fetch data directly** and **stream HTML** to clients.

### Example

```tsx
// app/page.tsx (Server Component)
export default async function Page() {
  const res = await fetch('https://api.example.com/posts');
  const posts = await res.json();
  return (
    <ul>
      {posts.map((p: any) => <li key={p.id}>{p.title}</li>)}
    </ul>
  );
}
```

Server components improve performance by:

* Reducing client JS
* Avoiding hydration for static parts
* Handling data fetching server-side

---

## 🖥️ Server-Side Rendering (SSR)

SSR pre-renders HTML for each request, improving SEO and initial load performance.

### Example with Next.js

```tsx
export async function getServerSideProps() {
  const data = await fetch('https://api.example.com/users').then(res => res.json());
  return { props: { users: data } };
}

export default function Users({ users }: any) {
  return <ul>{users.map((u: any) => <li key={u.id}>{u.name}</li>)}</ul>;
}
```

SSR is best for:

* Dynamic pages (user-specific, dashboards)
* SEO-driven applications (e-commerce, blogs)

---

## 🧱 Static Site Generation (SSG)

SSG builds pages **at compile time**. It’s ideal for content that rarely changes.

```tsx
export async function getStaticProps() {
  const posts = await fetch('https://api.example.com/posts').then(res => res.json());
  return { props: { posts } };
}
```

SSG provides lightning-fast load times since content is pre-rendered.

---

## 🔁 Incremental Static Regeneration (ISR)

ISR updates static content **periodically** without rebuilding the entire site.

```tsx
export async function getStaticProps() {
  const posts = await fetch('https://api.example.com/posts').then(r => r.json());
  return { props: { posts }, revalidate: 60 }; // re-generate every 60s
}
```

ISR blends static performance with dynamic updates.

---

## ⚡ Hybrid Rendering Strategies

Modern React frameworks like Next.js support hybrid rendering:

* Some pages SSR (dynamic)
* Others SSG (static)
* Some hydrated client-only (SPA sections)

Example structure:

```
/app/dashboard (SSR)
/app/blog (SSG)
/app/chat (Client-only)
```

---

## 🌍 Edge Rendering & Streaming

**Edge rendering** brings computation closer to users for faster response times.

* Platforms: Vercel Edge Functions, Cloudflare Workers.
* Benefits: low latency, automatic scaling.

### Streaming SSR Example

```tsx
import { renderToPipeableStream } from 'react-dom/server';

function handler(req, res) {
  const { pipe } = renderToPipeableStream(<App />, {
    onShellReady() {
      res.setHeader('Content-Type', 'text/html');
      pipe(res);
    }
  });
}
```

Streaming improves time-to-first-byte (TTFB) and user experience.

---

## 💾 Caching Strategies

Caching reduces redundant computation and API calls.

| Level       | Strategy         | Example                    |
| ----------- | ---------------- | -------------------------- |
| **Browser** | HTTP cache       | Cache-Control headers      |
| **Server**  | Data caching     | Redis, LRU caches          |
| **CDN**     | Edge caching     | Vercel, Cloudflare, Akamai |
| **React**   | Suspense caching | React Query, SWR           |

Example (SWR):

```tsx
import useSWR from 'swr';
const { data } = useSWR('/api/user', url => fetch(url).then(r => r.json()));
```

---

## 🚀 Performance Optimization Techniques

### Rendering Optimizations

* Use **memoization** (`React.memo`, `useMemo`, `useCallback`).
* Avoid unnecessary re-renders (React Profiler).
* Batch state updates in concurrent React.

### Network Optimizations

* Prefetch critical routes & assets.
* Use HTTP/2, Brotli compression.
* Optimize data fetching (pagination, caching).

### Build Optimizations

* Enable **tree shaking**.
* Optimize dependencies (lodash-es, date-fns).
* Split large libraries via dynamic import.

---

## 🖼️ Image Optimization

Use Next.js Image Component or libraries like `react-image`.

```tsx
import Image from 'next/image';
<Image src="/banner.jpg" alt="Banner" width={800} height={400} priority />
```

Features:

* Lazy loading
* Responsive sizes
* Automatic format conversion (WebP)

---

## 🔀 Code Splitting & Lazy Loading

Load code only when needed to reduce initial bundle size.

```tsx
const Profile = React.lazy(() => import('./Profile'));
<Suspense fallback={<Spinner />}>
  <Profile />
</Suspense>
```

Also use **Next.js dynamic imports**:

```tsx
import dynamic from 'next/dynamic';
const Chart = dynamic(() => import('../components/Chart'), { ssr: false });
```

---

## 🧩 Bundle Analysis & Tree Shaking

### Analyze bundle

```bash
npm install -D source-map-explorer
npx source-map-explorer build/static/js/*.js
```

### Remove unused imports

Tree shaking automatically removes dead code when using ESM modules (`import/export`).

### Keep bundle small

* Avoid unnecessary dependencies.
* Use code-splitting for routes and features.

---

## 🧠 Memory & Render Performance Debugging

### Tools

* React Profiler (Chrome DevTools > Components > Profiler)
* `why-did-you-render` for re-render debugging.
* Lighthouse for performance scores.

### Best Practices

* Avoid anonymous inline functions inside JSX.
* Use stable keys for lists.
* Defer non-critical effects.

---

## ☁️ Scalability & Deployment Best Practices

### Infrastructure

* Use **Vercel**, **Netlify**, or **AWS Amplify** for SSR/SSG deployments.
* Cache responses using **CDN edge**.
* Enable **incremental builds** for large content sites.

### CI/CD Integration

* Run automated Lighthouse and Jest tests before deployment.
* Use feature flags for progressive rollouts.

### Monitoring

* Integrate APM tools (Datadog, Sentry, LogRocket).
* Monitor metrics: FCP, TTFB, LCP, CLS.

---

## 🧾 Summary

* **RSC**, **SSR**, **SSG**, and **ISR** offer flexibility between dynamic and static rendering.
* Combine **edge streaming** and **caching** for global performance.
* Optimize code and assets using memoization, lazy loading, and bundling tools.
* Analyze, monitor, and iterate continuously for optimal performance.

---

Next: [Part VII – Testing & CI/CD →](#)
