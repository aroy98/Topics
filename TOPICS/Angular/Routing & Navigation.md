# 🅰️ Angular Mastery Series – Part IV: Routing & Navigation

---

## 🎯 Objective
Build robust navigation with the **standalone Angular Router**. You’ll configure routes, handle parameters and query strings, implement **lazy loading**, **guards**, **resolvers**, **preloading strategies**, and optimize UX with scroll restoration, route titles, and error handling.

---

## 🚀 1) Standalone Router Setup

Install a new app with standalone APIs:
```bash
ng new angular-routing --standalone
cd angular-routing
```

Define routes in `app.routes.ts`:
```typescript
import { Routes } from '@angular/router';
import { HomeComponent } from './home.component';

export const routes: Routes = [
  { path: '', component: HomeComponent, title: 'Home' },
  { path: 'about', loadComponent: () => import('./about.component').then(m => m.AboutComponent) },
  { path: '**', loadComponent: () => import('./not-found.component').then(m => m.NotFoundComponent), title: 'Not Found' }
];
```

Bootstrap router in `main.ts`:
```typescript
import { bootstrapApplication } from '@angular/platform-browser';
import { provideRouter, withInMemoryScrolling } from '@angular/router';
import { AppComponent } from './app/app.component';
import { routes } from './app/app.routes';

bootstrapApplication(AppComponent, {
  providers: [
    provideRouter(
      routes,
      withInMemoryScrolling({ scrollPositionRestoration: 'enabled', anchorScrolling: 'enabled' })
    )
  ]
});
```

Root template `app.component.html`:
```html
<nav>
  <a routerLink="/" routerLinkActive="active" [routerLinkActiveOptions]="{ exact: true }">Home</a>
  <a routerLink="/about" routerLinkActive="active">About</a>
</nav>
<router-outlet></router-outlet>
```

---

## 🧭 2) Route Parameters & Query Params

### Defining a Param Route
```typescript
{ path: 'users/:id', loadComponent: () => import('./users/user-detail.component').then(m => m.UserDetailComponent), title: 'User Detail' }
```

### Reading Params (signal-friendly pattern)
```typescript
import { Component, inject } from '@angular/core';
import { ActivatedRoute, Router } from '@angular/router';

@Component({ selector: 'app-user-detail', standalone: true, template: `<h2>User {{ id }}</h2>` })
export class UserDetailComponent {
  private route = inject(ActivatedRoute);
  private router = inject(Router);
  id = this.route.snapshot.paramMap.get('id');

  goToList() { this.router.navigate(['/users'], { queryParams: { page: 2 } }); }
}
```

### Observing Query Params
```typescript
this.route.queryParamMap.subscribe(q => {
  const page = q.get('page') ?? '1';
});
```

> Prefer `queryParamMap` over `queryParams` for string-safe access.

---

## 🧱 3) Nested Routes & Children

```typescript
export const routes: Routes = [
  {
    path: 'settings',
    loadComponent: () => import('./settings/settings.component').then(m => m.SettingsComponent),
    children: [
      { path: '', redirectTo: 'profile', pathMatch: 'full' },
      { path: 'profile', loadComponent: () => import('./settings/profile.component').then(m => m.ProfileComponent) },
      { path: 'security', loadComponent: () => import('./settings/security.component').then(m => m.SecurityComponent) }
    ]
  }
];
```

Child outlet in `settings.component.html`:
```html
<nav>
  <a routerLink="profile" routerLinkActive="active">Profile</a>
  <a routerLink="security" routerLinkActive="active">Security</a>
</nav>
<router-outlet></router-outlet>
```

---

## 💤 4) Lazy Loading (Components & Features)

### Lazy Component (recommended for standalone)
```typescript
{ path: 'reports', loadComponent: () => import('./reports/reports.component').then(m => m.ReportsComponent) }
```

### Lazy Feature Routes
```typescript
{ path: 'admin', loadChildren: () => import('./admin/admin.routes').then(m => m.ADMIN_ROUTES) }
```

`admin.routes.ts`:
```typescript
import { Routes } from '@angular/router';
export const ADMIN_ROUTES: Routes = [
  { path: '', loadComponent: () => import('./admin.component').then(m => m.AdminComponent) },
  { path: 'users', loadComponent: () => import('./users.component').then(m => m.UsersComponent) }
];
```

---

## 🛡️ 5) Guards (canActivate, canDeactivate, canMatch)

### Auth Guard (functional style)
```typescript
import { CanActivateFn, Router } from '@angular/router';
import { inject } from '@angular/core';

export const authGuard: CanActivateFn = () => {
  const router = inject(Router);
  const isLoggedIn = localStorage.getItem('token');
  return !!isLoggedIn || router.createUrlTree(['/login']);
};
```

Apply guard:
```typescript
{ path: 'dashboard', loadComponent: () => import('./dashboard.component').then(m => m.DashboardComponent), canActivate: [authGuard] }
```

### canDeactivate Guard
```typescript
import { CanDeactivateFn } from '@angular/router';

export const confirmLeaveGuard: CanDeactivateFn<unknown> = (component) => {
  const isDirty = (component as any)?.isDirty?.();
  return !isDirty || confirm('You have unsaved changes. Leave?');
};
```

### Route-level Matching (canMatch)
Useful for role-based access or A/B gating **before** loading the feature bundle.

```typescript
export const adminMatchGuard = () => localStorage.getItem('role') === 'admin';
{ path: 'admin', canMatch: [adminMatchGuard], loadChildren: () => import('./admin/admin.routes').then(m => m.ADMIN_ROUTES) }
```

---

## 🧳 6) Resolvers (Pre-Fetch Data Before Activation)

```typescript
import { ResolveFn } from '@angular/router';
import { inject } from '@angular/core';
import { UserApi } from './data/user.api';

export const userResolver: ResolveFn<any> = (route) => {
  const api = inject(UserApi);
  return api.getUser(route.paramMap.get('id')!);
};

{ path: 'users/:id', loadComponent: () => import('./user-detail.component').then(m => m.UserDetailComponent), resolve: { user: userResolver } }
```

In component:
```typescript
user = inject(ActivatedRoute).snapshot.data['user'];
```

---

## ⚡ 7) Preloading Strategies

Preload select lazy routes to improve perceived speed after the initial load.

### Built-in `PreloadAllModules`
```typescript
import { provideRouter, withPreloading, PreloadAllModules } from '@angular/router';

provideRouter(routes, withPreloading(PreloadAllModules));
```

### Custom Preloading (e.g., based on route data flag)
```typescript
import { PreloadingStrategy, Route } from '@angular/router';
import { Observable, of } from 'rxjs';

export class SelectivePreloading implements PreloadingStrategy {
  preload(route: Route, load: () => Observable<any>): Observable<any> {
    return route.data?.['preload'] ? load() : of(null);
  }
}
```

Configure:
```typescript
provideRouter(routes, withPreloading(SelectivePreloading));
```

Use on a route:
```typescript
{ path: 'reports', data: { preload: true }, loadComponent: () => import('./reports/reports.component').then(m => m.ReportsComponent) }
```

---

## 🧰 8) Navigation API & Extras

```typescript
import { Router } from '@angular/router';
this.router.navigate(['orders', id], {
  queryParams: { ref: 'email' },
  replaceUrl: false,
  state: { fromCampaign: true },
  fragment: 'details'
});
```

- `state` allows passing ephemeral data (accessible at `history.state`).
- `replaceUrl` avoids adding a new history entry.
- `fragment` scrolls to `id="details"` when `anchorScrolling` is enabled.

---

## 📡 9) Router Events & Analytics

```typescript
import { Router, NavigationEnd } from '@angular/router';
import { filter } from 'rxjs/operators';

router.events.pipe(filter(e => e instanceof NavigationEnd)).subscribe(e => {
  // send page view
});
```

---

## 🧯 10) Error Routes, Fallbacks, and Not-Found

```typescript
{ path: 'error', loadComponent: () => import('./error.component').then(m => m.ErrorComponent) },
{ path: '**', loadComponent: () => import('./not-found.component').then(m => m.NotFoundComponent) }
```

> Keep the wildcard `**` **last**. Use a friendly 404 with helpful links.

---

## 🧪 11) Testing Routing

- Component tests: use `RouterTestingHarness` or `RouterTestingModule` equivalents for standalone.
- Guard tests: test functions directly as pure functions.
- Resolver tests: mock API calls and assert emitted values.

Example (guard):
```typescript
it('redirects when not logged in', () => {
  localStorage.removeItem('token');
  const result = authGuard();
  expect(result instanceof UrlTree).toBeTrue();
});
```

---

## 🧠 Review Checklist
- [ ] Configure standalone routing with `provideRouter`.
- [ ] Use `loadComponent`/`loadChildren` for lazy features.
- [ ] Handle params, query params, and fragments.
- [ ] Protect routes with `canActivate`/`canDeactivate`/`canMatch`.
- [ ] Pre-fetch critical data via resolvers.
- [ ] Improve UX with preloading and scroll restoration.
- [ ] Provide 404/error routes and track navigation events.

---

## 📚 Next: Part V – Forms (Template-Driven & Reactive)
We’ll compare strategies, build complex **Reactive Forms** with validation, custom validators, async validation, **FormArray** patterns, and implement **ControlValueAccessor** for reusable inputs.