# 🅰️ Angular Mastery Series – Part III: Dependency Injection & Services

---

## 🎯 Objective
This part explores the **Angular Dependency Injection (DI) system**, covering:
- DI fundamentals and injector hierarchy
- Service creation and providers
- The `inject()` function for standalone setups
- Custom injection tokens
- Singleton and scoped services
- Common design patterns with services

---

## 🧩 1. What is Dependency Injection?
Dependency Injection (DI) is a design pattern where dependencies are provided to a class instead of being created inside it.

Example **without DI**:
```typescript
class UserService {
  private api = new ApiService(); // Tight coupling ❌
}
```

**With DI:**
```typescript
class UserService {
  constructor(private api: ApiService) {} // Dependency injected ✅
}
```

Angular manages these dependencies using an **Injector**.

---

## 🏗️ 2. Creating and Providing a Service

### Create a Service
```bash
ng generate service services/user
```

```typescript
import { Injectable } from '@angular/core';

@Injectable({ providedIn: 'root' })
export class UserService {
  getUsers() {
    return ['Alice', 'Bob', 'Charlie'];
  }
}
```

`providedIn: 'root'` means the service is **singleton** across the entire app.

### Providing in a Standalone Component
```typescript
@Component({
  selector: 'app-dashboard',
  standalone: true,
  providers: [UserService],
  template: `<p>Users: {{ users }}</p>`
})
export class DashboardComponent {
  users = this.userService.getUsers();
  constructor(private userService: UserService) {}
}
```
> This instance is unique to this component subtree.

---

## ⚙️ 3. Injector Hierarchy

Angular creates injectors at different levels:

| Level | Scope | Example |
|--------|--------|----------|
| **Root Injector** | Global singleton | `providedIn: 'root'` |
| **Component Injector** | Component tree | `providers: [UserService]` |
| **Module Injector** | Feature module (legacy) | `@NgModule.providers` |

### Example
If both parent and child components provide `LoggerService`, the child gets a **new instance**.

---

## 🧠 4. Using the `inject()` Function

In Angular standalone setups, you can inject services **without constructors**:

```typescript
import { Component, inject } from '@angular/core';
import { UserService } from './user.service';

@Component({
  selector: 'app-users',
  standalone: true,
  template: `<ul><li *ngFor="let user of users">{{ user }}</li></ul>`
})
export class UsersComponent {
  private userService = inject(UserService);
  users = this.userService.getUsers();
}
```

### When to use `inject()`
✅ Functional services or factories  
✅ Signals-based stores  
✅ Avoiding class constructor boilerplate

---

## 🔑 5. Custom Injection Tokens

Used to inject **non-class dependencies** like strings or configs.

```typescript
import { InjectionToken } from '@angular/core';

export const API_URL = new InjectionToken<string>('API_URL');
```

Provide it in configuration:
```typescript
bootstrapApplication(AppComponent, {
  providers: [
    { provide: API_URL, useValue: 'https://api.example.com' }
  ]
});
```

Inject it:
```typescript
constructor(@Inject(API_URL) private apiUrl: string) {
  console.log('API:', apiUrl);
}
```

---

## 🧩 6. Service Patterns

### Singleton Service
`providedIn: 'root'` ensures a single shared instance.

### Scoped Service
Provide in component-level providers array for isolated instances.

### Factory Provider
```typescript
providers: [
  {
    provide: LoggerService,
    useFactory: () => new LoggerService('DashboardContext')
  }
]
```

### Alias Provider
```typescript
providers: [
  { provide: BaseLogger, useExisting: LoggerService }
]
```

### Value Provider
```typescript
providers: [
  { provide: APP_VERSION, useValue: '1.0.0' }
]
```

---

## 🧮 7. Using Services with RxJS

```typescript
@Injectable({ providedIn: 'root' })
export class CounterService {
  private count = new BehaviorSubject<number>(0);
  count$ = this.count.asObservable();

  increment() { this.count.next(this.count.value + 1); }
}
```

```typescript
@Component({
  selector: 'app-counter',
  standalone: true,
  template: `<button (click)="increment()">+</button>{{ count() }}`
})
export class CounterComponent {
  private counter = inject(CounterService);
  count = signal(0);

  constructor() {
    this.counter.count$.subscribe(v => this.count.set(v));
  }

  increment() { this.counter.increment(); }
}
```

---

## 🧠 Review Checklist
- [ ] Understand Angular’s DI mechanism and hierarchy.
- [ ] Use `@Injectable()` and `providedIn` wisely.
- [ ] Use component `providers` for scoped services.
- [ ] Inject tokens using `@Inject()` or `inject()`.
- [ ] Leverage RxJS in service-based state management.

---

## 📚 Next: Part IV – Routing & Navigation
Covers **standalone Router setup**, **lazy loading**, **guards**, **resolvers**, and **navigation strategies**.

