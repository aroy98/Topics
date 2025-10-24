# 🅰️ Angular Mastery Series – Part VII: HTTP, Interceptors & Data Access

---

## 🎯 Objective
Learn how to manage **HTTP communication** in Angular using `HttpClient`. We’ll cover **typed APIs**, **interceptors** (auth, retry, caching, logging), **error handling**, **pagination**, and **data access design patterns** for scalable applications.

---

## ⚙️ 1) Setting Up HttpClient

```ts
import { bootstrapApplication } from '@angular/platform-browser';
import { provideHttpClient, withInterceptorsFromDi } from '@angular/common/http';
import { AppComponent } from './app/app.component';

bootstrapApplication(AppComponent, {
  providers: [provideHttpClient(withInterceptorsFromDi())]
});
```

Use `withInterceptorsFromDi()` to support DI-based interceptors.

---

## 🧩 2) Making HTTP Calls

```ts
import { HttpClient } from '@angular/common/http';
import { Injectable, inject } from '@angular/core';
import { Observable } from 'rxjs';

export interface User { id: number; name: string; email: string; }

@Injectable({ providedIn: 'root' })
export class UserApi {
  private http = inject(HttpClient);
  private baseUrl = 'https://jsonplaceholder.typicode.com/users';

  getAll(): Observable<User[]> {
    return this.http.get<User[]>(this.baseUrl);
  }

  getById(id: number): Observable<User> {
    return this.http.get<User>(`${this.baseUrl}/${id}`);
  }

  create(user: Partial<User>): Observable<User> {
    return this.http.post<User>(this.baseUrl, user);
  }

  update(id: number, user: Partial<User>): Observable<User> {
    return this.http.put<User>(`${this.baseUrl}/${id}`, user);
  }

  delete(id: number): Observable<void> {
    return this.http.delete<void>(`${this.baseUrl}/${id}`);
  }
}
```

### In component:
```ts
@Component({ selector: 'app-users', standalone: true, template: `
  <ul><li *ngFor="let u of users$ | async">{{ u.name }}</li></ul>
`})
export class UsersComponent {
  private api = inject(UserApi);
  users$ = this.api.getAll();
}
```

---

## 🛡️ 3) Interceptors Overview
Interceptors modify requests/responses globally for:
- ✅ Authentication (JWT, tokens)
- ⚡ Retry logic
- 💾 Caching
- 🪵 Logging
- 🚨 Error handling

### Basic Structure
```ts
import { HttpInterceptorFn } from '@angular/common/http';

export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const token = localStorage.getItem('token');
  const authReq = token ? req.clone({ setHeaders: { Authorization: `Bearer ${token}` } }) : req;
  return next(authReq);
};
```

Register in `main.ts`:
```ts
provideHttpClient(withInterceptors([authInterceptor]));
```

---

## 🔁 4) Retry & Error Handling Interceptor

```ts
import { HttpInterceptorFn, HttpErrorResponse } from '@angular/common/http';
import { catchError, retry, throwError } from 'rxjs';

export const retryInterceptor: HttpInterceptorFn = (req, next) =>
  next(req).pipe(
    retry(2),
    catchError((err: HttpErrorResponse) => {
      console.error('Error:', err.message);
      return throwError(() => new Error('Something went wrong.'));
    })
  );
```

---

## 💾 5) Caching Interceptor (In-Memory)

```ts
import { HttpInterceptorFn } from '@angular/common/http';
import { of, tap } from 'rxjs';

const cache = new Map<string, any>();

export const cacheInterceptor: HttpInterceptorFn = (req, next) => {
  if (req.method !== 'GET') return next(req);
  const cached = cache.get(req.urlWithParams);
  if (cached) return of(cached);

  return next(req).pipe(tap(event => cache.set(req.urlWithParams, event)));
};
```

> In production, consider dedicated caching libraries or service workers (for PWAs).

---

## 🪵 6) Logging Interceptor

```ts
export const logInterceptor: HttpInterceptorFn = (req, next) => {
  console.log('➡️ Request:', req.method, req.url);
  const start = performance.now();
  return next(req).pipe(tap(() => console.log(`✅ Done: ${req.url} (${performance.now() - start}ms)`)));
};
```

---

## 🚨 7) Global HTTP Error Handling

Create a dedicated error service:
```ts
@Injectable({ providedIn: 'root' })
export class ErrorHandlerService {
  handle(error: any) {
    alert('Error occurred: ' + (error.message || 'Unknown'));
  }
}
```

Use with interceptor:
```ts
export const globalErrorInterceptor: HttpInterceptorFn = (req, next) =>
  next(req).pipe(catchError(err => {
    inject(ErrorHandlerService).handle(err);
    return throwError(() => err);
  }));
```

---

## 📊 8) Pagination & Infinite Scroll

### Example API
```ts
getPaginated(page: number, limit: number): Observable<User[]> {
  return this.http.get<User[]>(`${this.baseUrl}?_page=${page}&_limit=${limit}`);
}
```

### Infinite Scroll Logic
```ts
loadMore$ = new Subject<void>();
page = 1;
users$ = this.loadMore$.pipe(
  startWith(null),
  switchMap(() => this.api.getPaginated(this.page++, 10)),
  scan((acc, val) => [...acc, ...val], [] as User[])
);
```

---

## 🧩 9) Type Safety & Data Models

Use interfaces and DTOs for safe and explicit contracts:
```ts
export interface ApiResponse<T> {
  data: T;
  status: 'success' | 'error';
  message?: string;
}
```

Strongly type HTTP responses:
```ts
getProducts(): Observable<ApiResponse<Product[]>> {
  return this.http.get<ApiResponse<Product[]>>(this.apiUrl);
}
```

---

## 🧰 10) Best Practices
✅ Centralize API logic into service classes (e.g., `UserApi`, `ProductApi`).  
✅ Use interceptors for cross-cutting concerns (auth, logging, caching).  
✅ Retry only **idempotent** GET requests.  
✅ Keep API URLs in a config or environment file.  
✅ Prefer Observables over Promises for stream control.  
✅ Handle 401/403 globally to redirect to login.  
✅ Avoid large responses—use pagination or lazy loading.

---

## 🧠 Review Checklist
- [ ] Use typed HTTP requests via `HttpClient`.
- [ ] Apply interceptors for auth, retry, logging, caching.
- [ ] Handle global errors gracefully.
- [ ] Support pagination & infinite scroll patterns.
- [ ] Keep data access centralized and type-safe.
- [ ] Use RxJS operators for transformation & retry.

---

## 📚 Next: Part VIII – Performance & Optimization
We’ll dive into **Change Detection Strategies**, **Signals & Computed Values**, **TrackBy**, **Virtualization**, **Code Splitting**, and **Bundle Analysis** to make Ang