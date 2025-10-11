# Angular Mastery Series — Questions, Explanations, and Examples (Angular 17+)

This comprehensive, **frontend-only** guide covers Angular from fundamentals to production. It uses **Angular 17+ features** including **Standalone Components** and **Signals**, with **HTTP** integration, **RxJS**, **Routing**, **Guards**, **Interceptors**, **Testing**, and **Deployment**. Each part includes **questions**, **explanations**, and **code examples**.

---

## 🧩 Part I — Fundamentals (TS, CLI, Project Structure)

### **Question 1:** What changed with Angular 15–17 regarding modules?

**Explanation:** Angular now supports **Standalone Components** — components, directives, and pipes can be bootstrapped without `NgModule`. `bootstrapApplication()` replaces `platformBrowserDynamic().bootstrapModule(AppModule)`.

**Example — Standalone Bootstrap**

```ts
// main.ts (Angular 17)
import { bootstrapApplication } from '@angular/platform-browser';
import { provideRouter } from '@angular/router';
import { AppComponent } from './app/app.component';
import { routes } from './app/app.routes';

bootstrapApplication(AppComponent, {
  providers: [provideRouter(routes)]
});
```

**Example — Standalone Component**

```ts
// app.component.ts
import { Component, signal } from '@angular/core';

@Component({
  selector: 'app-root',
  standalone: true,
  template: `
    <h1>Angular 17 Mastery</h1>
    <p>Count: {{ count() }}</p>
    <button (click)="inc()">Increment</button>
  `
})
export class AppComponent {
  count = signal(0);
  inc() { this.count.update(c => c + 1); }
}
```

---

## 🔁 Part II — Components, Templates, Signals & Change Detection

### **Question 2:** What are **Signals** and when to use them vs RxJS?

**Explanation:** Signals are **fine-grained reactivity** primitives for local state. Use Signals for component state and computed values; use **RxJS** for **async streams**, HTTP, and event composition.

**Example — Computed & Effect**

```ts
import { Component, computed, effect, signal } from '@angular/core';

@Component({ selector: 'counter', standalone: true, template: `{{ label() }}` })
export class CounterComponent {
  count = signal(1);
  label = computed(() => `x2: ${this.count() * 2}`);
  log = effect(() => console.log('count:', this.count()));
}
```

### **Question 3:** How does change detection interact with Signals?

**Explanation:** Signal reads are tracked. When a Signal updates, only the reading views re-render. Combine with `OnPush` (legacy) or rely on Signals for granular updates.

---

## 🧭 Part III — Routing, Guards, Lazy Loading

### **Question 4:** How to configure routes with Standalone APIs?

**Explanation:** Use `provideRouter()` and lazy-load standalone components via `loadComponent`/`loadChildren`.

**Example — Routes**

```ts
// app.routes.ts
import { Routes } from '@angular/router';

export const routes: Routes = [
  { path: '', loadComponent: () => import('./home.component').then(m => m.HomeComponent) },
  { path: 'products', loadChildren: () => import('./products.routes').then(m => m.PRODUCTS_ROUTES) },
  { path: '**', redirectTo: '' }
];
```

**Example — Feature Route Config**

```ts
// products.routes.ts
import { Routes } from '@angular/router';
export const PRODUCTS_ROUTES: Routes = [
  { path: '', loadComponent: () => import('./products.component').then(m => m.ProductsComponent) }
];
```

### **Question 5:** How to protect routes with Guards?

**Explanation:** Use functional guards returning `boolean|UrlTree|Observable|Promise`.

```ts
// auth.guard.ts
import { inject } from '@angular/core';
import { CanActivateFn, Router } from '@angular/router';
import { AuthService } from './auth.service';

export const authGuard: CanActivateFn = () => {
  const auth = inject(AuthService);
  const router = inject(Router);
  return auth.isLoggedIn() ? true : router.parseUrl('/login');
};
```

---

## 🌐 Part IV — HTTP, RxJS, Interceptors & Error Handling

### **Question 6:** Best practices for HTTP in Angular?

**Explanation:** Use `HttpClient` in services; model responses; centralize **interceptors** for auth headers, retries, logging; use RxJS operators for composition.

**Example — Service with HttpClient**

```ts
// products.service.ts (standalone service)
import { Injectable, inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

export interface Product { id: number; name: string; price: number; }

@Injectable({ providedIn: 'root' })
export class ProductsService {
  private http = inject(HttpClient);
  private base = 'https://api.example.com';
  list(): Observable<Product[]> { return this.http.get<Product[]>(`${this.base}/products`); }
  get(id: number) { return this.http.get<Product>(`${this.base}/products/${id}`); }
}
```

**Example — HTTP Interceptor**

```ts
// auth.interceptor.ts
import { HttpInterceptorFn } from '@angular/common/http';

export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const cloned = req.clone({ setHeaders: { Authorization: `Bearer TOKEN` } });
  return next(cloned);
};
```

**Provide Interceptor**

```ts
// main.ts
import { provideHttpClient, withInterceptors } from '@angular/common/http';
bootstrapApplication(AppComponent, {
  providers: [
    provideRouter(routes),
    provideHttpClient(withInterceptors([authInterceptor]))
  ]
});
```

**Example — Retry + Error Handling**

```ts
import { catchError, retry, throwError } from 'rxjs';

this.http.get('/endpoint').pipe(
  retry(2),
  catchError(err => throwError(() => new Error('Failed to load')))
);
```

---

## 🧰 Part V — Forms (Reactive), Validation & UX Patterns

### **Question 7:** Why prefer **Reactive Forms**?

**Explanation:** Reactive Forms offer immutable, testable state trees and granular validation, ideal for complex forms.

**Example — Reactive Form**

```ts
import { Component } from '@angular/core';
import { ReactiveFormsModule, FormBuilder, Validators } from '@angular/forms';

@Component({
  standalone: true,
  selector: 'user-form',
  imports: [ReactiveFormsModule],
  template: `
    <form [formGroup]="form" (ngSubmit)="save()">
      <input formControlName="email" placeholder="Email" />
      <div *ngIf="form.controls.email.invalid && form.controls.email.touched">Invalid email</div>
      <button type="submit" [disabled]="form.invalid">Save</button>
    </form>
  `
})
export class UserFormComponent {
  form = this.fb.group({ email: ['', [Validators.required, Validators.email]] });
  constructor(private fb: FormBuilder) {}
  save(){ console.log(this.form.value); }
}
```

**Async Validators & UX**: debounce value changes, show server errors, disable submit while pending.

---

## 📦 Part VI — State & Architecture (Signals, RxJS, Smart/Dumb)

### **Question 8:** How to choose between **Signals** and **NgRx**?

**Explanation:**

* **Signals**: Local/UI state, derived/computed values, component reactivity.
* **NgRx** (or RxJS services): Global/app state, server cache, complex orchestration.

**Example — Service with Signals**

```ts
import { Injectable, signal, computed } from '@angular/core';

@Injectable({ providedIn: 'root' })
export class CartStore {
  private items = signal<{id:number; name:string; price:number}[]>([]);
  total = computed(() => this.items().reduce((s,i)=>s+i.price,0));
  add(item: {id:number; name:string; price:number}) { this.items.update(xs => [...xs, item]); }
  remove(id:number) { this.items.update(xs => xs.filter(i => i.id!==id)); }
}
```

---

## 🧪 Part VII — Testing (Karma/Jasmine or Jest), Accessibility & CI/CD

### **Question 9:** How to test standalone components and services?

**Explanation:** Use `TestBed` with `imports` for standalone components; use spies/mocks for services.

**Example — Component Test**

```ts
import { TestBed } from '@angular/core/testing';
import { AppComponent } from './app.component';

it('renders title', async () => {
  await TestBed.configureTestingModule({ imports: [AppComponent] }).compileComponents();
  const fixture = TestBed.createComponent(AppComponent);
  fixture.detectChanges();
  expect(fixture.nativeElement.querySelector('h1').textContent).toContain('Angular 17 Mastery');
});
```

### **Question 10:** CI/CD basics

* Lint (`eslint`), unit tests, build artifacts, preview deploys (Vercel/Netlify) or static hosting (S3+CloudFront).

**Example — GitHub Actions**

```yaml
name: Angular CI
on: [push, pull_request]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '20' }
      - run: npm ci
      - run: npm run lint --if-present
      - run: npm test -- --watch=false --browsers=ChromeHeadless
      - run: npm run build -- --configuration=production
```

---

## 🚀 Part VIII — Performance, Accessibility & Deployment

### **Question 11:** Performance tactics

* **Standalone + Signals** reduce change detection overhead
* **Lazy-load** routes/components, **defer** non-critical work
* Use `trackBy` in `*ngFor`, `OnPush` where applicable
* Prefetch data, memoize selectors/computeds

### **Question 12:** Deployment

* `ng build --configuration=production` produces static assets.
* Host on Nginx/S3/CloudFront/Vercel/Netlify — configure SPA fallback to `index.html`.

**Example — Nginx SPA Fallback**

```nginx
location / {
  try_files $uri $uri/ /index.html;
}
```

---

## ✅ Summary Checklist

| Area             | Focus                                             |
| ---------------- | ------------------------------------------------- |
| **Architecture** | Standalone components, Signals, functional guards |
| **HTTP**         | HttpClient, interceptors, retry, error handling   |
| **State**        | Signals for local; RxJS/NgRx for global           |
| **Forms**        | Reactive Forms, async validation                  |
| **Routing**      | Lazy loading, loadComponent/loadChildren          |
| **Testing**      | TestBed with standalone, CI via GitHub Actions    |
| **Perf**         | Lazy-load, trackBy, memoization, SPA caching      |
| **Deploy**       | Static hosting, SPA fallback, caching headers     |

---

### Practice Q&A (Quickfire)

1. **What are Signals?** Fine-grained reactive primitives for local state.
2. **When to use interceptors?** Cross-cutting concerns: auth, logging, retries.
3. **How to lazy-load standalone?** `loadComponent`/`loadChildren` in routes.
4. **Why Reactive Forms?** Predictable, testable, scalable.
5. **How to handle 401 globally?** Interceptor: redirect to `/login`.

**End of Document — Angular Mastery Series (Angular 17+, Standalone & Signals)**
