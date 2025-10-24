# 🅰️ Angular Mastery Series – Part VIII: Performance & Optimization

---

## 🎯 Objective
Make Angular apps **fast and smooth**. You’ll learn how to:
- Optimize **change detection** (`OnPush`, signals, manual strategies)
- Use **deferrable views** (`@defer`) and **lazy loading**
- Apply **trackBy**, **virtualization**, and **NgOptimizedImage**
- Tune runtime with **event/run coalescing** and **zoned/zoneless** options
- Shrink bundles, analyze builds, and cache effectively (HTTP, SW/PWA)

---

## ⚡ 1) Change Detection – Strategies & Patterns

### `Default` vs `OnPush`
- **Default**: Checks every component on async tasks (Zone.js).
- **OnPush**: Checks only when **@Input changes**, an **event** in the component occurs, or **signals**/`Observable` emissions bound via `async` pipe update.

```ts
@Component({
  selector: 'app-stats',
  standalone: true,
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `{{ total() }}`
})
export class StatsComponent {
  // Prefer signals/computed for fine‑grained updates
  items = signal<number[]>([]);
  total = computed(() => this.items().reduce((a, b) => a + b, 0));
}
```

### Manual CD – `ChangeDetectorRef`
```ts
constructor(private cdr: ChangeDetectorRef) {}
// After a non-Angular callback finishes:
this.cdr.markForCheck(); // schedule check for OnPush component
```

> Avoid `detectChanges()` loops. Prefer `markForCheck()` with **OnPush**.

---

## 🧵 2) Signals for Fine-Grained Reactivity
Signals update **only the consumers**.

```ts
const first = signal(2);
const second = signal(3);
const sum = computed(() => first() + second());

effect(() => console.log('sum changed:', sum()));
```

**Guidelines**
- Use **signals** in performance‑critical components.
- Derive expensive values with `computed` and memoize.
- Prefer `toSignal(obs$)` for bridging Observables from services/NgRx.

---

## 💤 3) Deferrable Views – `@defer`
Load heavy UI **later** (viewport, interaction, idle, timer).

```html
@defer (on viewport; prefetch on idle) {
  <chart-widget></chart-widget>
} @placeholder {
  <app-skeleton></app-skeleton>
} @loading {
  <p>Loading...</p>
} @error {
  <p>Couldn’t load chart.</p>
}
```

> Combine with route/component **lazy loading** for maximal savings.

---

## 🔀 4) Lazy Loading – Routes & Components

```ts
{ path: 'admin', loadChildren: () => import('./admin/admin.routes').then(m => m.ADMIN_ROUTES) }
{ path: 'report', loadComponent: () => import('./report/report.component').then(m => m.ReportComponent) }
```

Split rarely used features and large widgets (charts/editors) into separate chunks.

---

## 🔁 5) Lists: `trackBy` & Virtual Scrolling

### `trackBy`
```html
<li *ngFor="let item of items; trackBy: trackById">{{ item.name }}</li>
```
```ts
trackById = (_: number, item: { id: string }) => item.id;
```

### Virtual Scroll (CDK)
```html
<cdk-virtual-scroll-viewport itemSize="48" class="h-96 w-full">
  <div *cdkVirtualFor="let user of users; trackBy: trackById">{{ user.name }}</div>
</cdk-virtual-scroll-viewport>
```
> Only renders visible rows → massive DOM savings.

---

## 🖼️ 6) Images & Media
- Use **`NgOptimizedImage`** for automatic lazy loading and correct sizing.
- Serve responsive sources (`srcset`, `sizes`).
- Prefer modern formats (AVIF/WebP), compress assets.

```ts
import { NgOptimizedImage } from '@angular/common';
@Component({
  standalone: true,
  imports: [NgOptimizedImage],
  template: `<img ngSrc="/assets/hero.webp" width="1200" height="600" priority>`
})
export class Hero {}
```

---

## 🧠 7) Runtime Tuning – Coalescing & Zones

Enable event/run coalescing to batch CD runs:
```ts
import { provideZoneChangeDetection } from '@angular/core';

bootstrapApplication(AppComponent, {
  providers: [provideZoneChangeDetection({ eventCoalescing: true, runCoalescing: true })]
});
```

**Zoneless** (advanced): run without Zone.js and drive updates via signals/`markForCheck` where applicable. Adopt gradually in leaf components.

---

## 🧭 8) Template & Component Hygiene
- Avoid calling methods directly in templates for computed values → use **signals/computed** or **pure pipes**.
- Keep components **OnPush** and inputs **immutable** (spread new objects/arrays).
- Use `async` pipe (auto‑unsubscribe) instead of manual `subscribe()` in templates.
- Prefer smaller, focused components; lift expensive logic to services/workers.

---

## 📦 9) Build Optimizations & Bundle Analysis

### Production Build
```bash
ng build --configuration=production
```
- Minification, dead code elimination, CSS optimization.

### Analyze Bundles
```bash
# Generate stats (Angular CLI)
ng build --configuration=production --stats-json
# Explore
npx source-map-explorer dist/**/browser/*.js
```

**Tips**
- Remove unused polyfills; target modern browsers when possible.
- Prefer **ESM** libraries and **tree‑shakable** imports.
- Audit dependencies; avoid shipping large UI kits if unused.

---

## 🌐 10) Network & Caching
- Add HTTP caching headers server‑side; implement client caching interceptors for GET.
- Use **Service Worker** (PWA) for offline/asset caching and faster re-visits.
- Defer non‑critical requests; hydrate SSR with **TransferState** to avoid duplicate HTTP calls on client.

```ts
// SSR: write to TransferState on server; read on client
```

---

## 🧮 11) Concurrency & Workers
Offload CPU-heavy tasks (parsing, image processing) to **Web Workers** to keep UI responsive.

```bash
ng generate web-worker workers/heavy
```

---

## 🧪 12) Perf Measurement & Guardrails
- **Lighthouse** / **Web Vitals**: LCP, CLS, INP.
- Angular DevTools: CD cycles, component profiling.
- Set **budgets** in `angular.json` to fail CI on bloat.

```json
{
  "budgets": [{
    "type": "bundle",
    "name": "main",
    "maximumWarning": "250kb",
    "maximumError": "300kb"
  }]
}
```

---

## 🧠 Review Checklist
- [ ] Use `OnPush`, signals, and `markForCheck` appropriately.
- [ ] Defer heavy UI with `@defer` and lazy loading.
- [ ] Apply `trackBy` and CDK virtual scroll for big lists.
- [ ] Optimize images with `NgOptimizedImage`.
- [ ] Enable event/run coalescing.
- [ ] Analyze bundles; enforce budgets in CI.
- [ ] Cache smartly (HTTP, SW/PWA); leverage TransferState with SSR.

---

## 📚 Next: Part IX – Testing, Tooling & CI/CD
We’ll cover **unit testing (Jest/Jasmine)**, **component harnesses**, **E2E (Cypress/Playwright)**, **Storybook for Angular**, linting/formatting, and **CI/CD** with GitHub Actions/Nx.

