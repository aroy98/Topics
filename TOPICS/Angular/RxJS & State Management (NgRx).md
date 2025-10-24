# 🅰️ Angular Mastery Series – Part VI: RxJS & State Management (NgRx)

---

## 🎯 Objective
Master **reactive data flows** with **RxJS** and build scalable state using **NgRx Store/Effects/Entity**. Learn the **facade pattern**, **selectors**, **signals interop**, and **testing**.

---

## 🧪 1) RxJS Essentials for Angular

### Common Operators
- **Creation**: `of`, `from`, `timer`, `interval`
- **Transformation**: `map`, `switchMap`, `mergeMap`, `concatMap`, `exhaustMap`
- **Filtering**: `filter`, `debounceTime`, `distinctUntilChanged`, `take`, `takeUntil`
- **Combination**: `combineLatest`, `withLatestFrom`, `forkJoin`
- **Error Handling**: `catchError`, `retry`, `retryWhen`
- **Multicasting**: `shareReplay({ bufferSize: 1, refCount: true })`

### Example: Search Autosuggest
```ts
search$ = new Subject<string>();
results$ = this.search$.pipe(
  debounceTime(300),
  distinctUntilChanged(),
  switchMap(q => this.api.search(q).pipe(
    catchError(() => of([]))
  )),
  shareReplay({ bufferSize: 1, refCount: true })
);
```

> Prefer `switchMap` for live search; it cancels stale requests.

---

## 🏛️ 2) NgRx – Core Concepts
- **Actions** – describe *what happened*.
- **Reducers** – pure functions that update *state*.
- **Selectors** – query slices of state efficiently.
- **Effects** – handle side effects (HTTP, storage, analytics).
- **Entity** – normalized collections with adapters.

Install:
```bash
ng add @ngrx/store @ngrx/effects @ngrx/entity @ngrx/store-devtools
```

---

## 🧩 3) Feature State – Example: Products

### Actions (`products.actions.ts`)
```ts
import { createActionGroup, props, emptyProps } from '@ngrx/store';

export const ProductsActions = createActionGroup({
  source: 'Products',
  events: {
    'Load': emptyProps(),
    'Load Success': props<{ products: Product[] }>(),
    'Load Failure': props<{ error: string }>(),
    'Select': props<{ id: string }>(),
  }
});
```

### Entity + Reducer (`products.reducer.ts`)
```ts
import { createEntityAdapter, EntityState } from '@ngrx/entity';
import { createReducer, on } from '@ngrx/store';

export interface Product { id: string; title: string; price: number }
export interface ProductsState extends EntityState<Product> {
  loaded: boolean;
  selectedId: string | null;
  error?: string | null;
}

const adapter = createEntityAdapter<Product>({ selectId: p => p.id });
const initialState: ProductsState = adapter.getInitialState({ loaded: false, selectedId: null, error: null });

export const productsReducer = createReducer(
  initialState,
  on(ProductsActions.Load, state => ({ ...state, loaded: false })),
  on(ProductsActions.LoadSuccess, (state, { products }) => adapter.setAll(products, { ...state, loaded: true })),
  on(ProductsActions.LoadFailure, (state, { error }) => ({ ...state, error })),
  on(ProductsActions.Select, (state, { id }) => ({ ...state, selectedId: id }))
);

export const { selectAll, selectEntities, selectIds, selectTotal } = adapter.getSelectors();
```

### Selectors (`products.selectors.ts`)
```ts
import { createFeatureSelector, createSelector } from '@ngrx/store';

export const selectProductsState = createFeatureSelector<ProductsState>('products');

export const selectAllProducts = createSelector(selectProductsState, selectAll);
export const selectLoaded = createSelector(selectProductsState, s => s.loaded);
export const selectSelectedId = createSelector(selectProductsState, s => s.selectedId);
export const selectSelectedProduct = createSelector(
  selectAllProducts,
  selectSelectedId,
  (products, id) => products.find(p => p.id === id) || null
);
```

### Effects (`products.effects.ts`)
```ts
import { Actions, createEffect, ofType } from '@ngrx/effects';
import { inject, Injectable } from '@angular/core';
import { map, catchError, switchMap } from 'rxjs/operators';
import { of } from 'rxjs';

@Injectable()
export class ProductsEffects {
  private actions$ = inject(Actions);
  private api = inject(ProductsApi);

  load$ = createEffect(() => this.actions$.pipe(
    ofType(ProductsActions.Load),
    switchMap(() => this.api.getAll().pipe(
      map(products => ProductsActions.LoadSuccess({ products })),
      catchError(err => of(ProductsActions.LoadFailure({ error: String(err) })))
    ))
  ));
}
```

### Feature Registration (standalone)
```ts
import { provideState, provideEffects } from '@ngrx/store';
bootstrapApplication(AppComponent, {
  providers: [
    provideState('products', productsReducer),
    provideEffects(ProductsEffects)
  ]
});
```

---

## 🏷️ 4) Facade Pattern (Optional but Recommended)

Create a single service per feature that exposes **observables** and **dispatch methods**, decoupling components from NgRx internals.

```ts
@Injectable({ providedIn: 'root' })
export class ProductsFacade {
  private store = inject(Store);

  all$ = this.store.select(selectAllProducts);
  loaded$ = this.store.select(selectLoaded);
  selected$ = this.store.select(selectSelectedProduct);

  load() { this.store.dispatch(ProductsActions.Load()); }
  select(id: string) { this.store.dispatch(ProductsActions.Select({ id })); }
}
```

Usage in component:
```ts
@Component({ selector: 'app-products', standalone: true, template: `
  <button (click)="facade.load()">Load</button>
  <ul><li *ngFor="let p of (facade.all$ | async)" (click)="facade.select(p.id)">{{ p.title }}</li></ul>
`})
export class ProductsComponent { constructor(public facade: ProductsFacade) {} }
```

---

## 🔁 5) Signals Interop
Use `@angular/core/rxjs-interop` to bridge signals and store selectors.

```ts
import { toSignal } from '@angular/core/rxjs-interop';

allSig = toSignal(this.facade.all$, { initialValue: [] });
selectedSig = toSignal(this.facade.selected$, { initialValue: null });
```

You can also drive components with signals-only facades while keeping NgRx under the hood.

---

## ⚙️ 6) Side Effects Patterns
- **Flattening strategy**: choose `switchMap` (cancel), `concatMap` (queue), `mergeMap` (parallel), `exhaustMap` (ignore during active).
- **Global error handling** inside effects; surface UI-friendly messages.
- **Idempotent actions**: prevent duplicate loads with `withLatestFrom(selectLoaded)`.

```ts
loadIfNeeded$ = createEffect(() => this.actions$.pipe(
  ofType(ProductsActions.Load),
  withLatestFrom(this.store.select(selectLoaded)),
  filter(([, loaded]) => !loaded),
  switchMap(() => this.api.getAll().pipe(
    map(products => ProductsActions.LoadSuccess({ products })),
    catchError(err => of(ProductsActions.LoadFailure({ error: String(err) })))
  ))
));
```

---

## 🧰 7) Entity Patterns
- Keep collections normalized with `@ngrx/entity`.
- Use `upsertOne`, `addMany`, `updateOne`, `removeOne` for CRUD.
- Derive views with selectors; avoid ad-hoc array scans in components.

```ts
on(ProductsActions.Update, (state, { update }) => adapter.updateOne(update, state));
```

---

## 🧪 8) Testing

### Reducer (pure function)
```ts
it('sets loaded on LoadSuccess', () => {
  const state = productsReducer(undefined, ProductsActions.LoadSuccess({ products: [] }));
  expect(state.loaded).toBeTrue();
});
```

### Selector
```ts
it('selectSelectedProduct', () => {
  const state = { products: adapter.setAll([{ id:'1', title:'A', price:1 }], { ...initialState, selectedId: '1' }) } as any;
  expect(selectSelectedProduct.projector(selectAll(state.products), '1')?.title).toBe('A');
});
```

### Effect (marble-friendly)
Use `provideMockActions` and Jasmine marbles or RxJS TestScheduler.

```ts
it('emits LoadSuccess on success', () => {
  actions$ = hot('-a', { a: ProductsActions.Load() });
  spyOn(api, 'getAll').and.returnValue(cold('-b', { b: [] }));
  const expected = cold('--c', { c: ProductsActions.LoadSuccess({ products: [] }) });
  expect(effects.load$).toBeObservable(expected);
});
```

---

## 📈 9) DevTools & Performance
- Enable Store DevTools in dev only.
- Memoize selectors; compute heavy derivations in selectors, not components.
- Avoid giant global stores—prefer **feature slices** and **facades**.
- Keep actions semantic and minimal (no large payloads in every action).

DevTools (standalone):
```ts
import { provideStoreDevtools } from '@ngrx/store-devtools';

bootstrapApplication(AppComponent, {
  providers: [
    provideState('products', productsReducer),
    provideEffects(ProductsEffects),
    provideStoreDevtools({ maxAge: 25, logOnly: true })
  ]
});
```

---

## 🧠 Review Checklist
- [ ] Model async flows with RxJS operators.
- [ ] Structure state with actions, reducers, selectors, effects.
- [ ] Normalize collections using Entity adapters.
- [ ] Decouple UI via the **facade pattern**.
- [ ] Bridge to **signals** with `toSignal`.
- [ ] Test reducers, selectors, and effects in isolation.

---

## 📚 Next: Part VII – HTTP, Interceptors & Data Access
We’ll cover `HttpClient` best practices, **interceptors** (auth, retry, caching), error handling strategies, typed APIs,