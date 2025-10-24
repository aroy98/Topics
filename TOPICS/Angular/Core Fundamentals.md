# 🅰️ Angular Mastery Series – Part I: Core Fundamentals

---

## 🎯 Objective
This section lays the foundation of Angular, focusing on **TypeScript essentials**, **Angular project anatomy**, **standalone components**, **templates**, **data binding**, **directives**, **pipes**, and **change detection**.

By the end of this part, you’ll be able to:
- Build and bootstrap a standalone Angular app.
- Understand how Angular components render and react to data.
- Apply directives, pipes, and bindings efficiently.

---

## 🧱 1. TypeScript Essentials for Angular
Angular is built on TypeScript — understanding its syntax and typing is key.

### Core TypeScript Concepts:
```typescript
// Type annotations
let title: string = 'Angular Mastery';

// Interfaces
interface User {
  name: string;
  isActive: boolean;
}

// Classes & Access Modifiers
class Student {
  constructor(public name: string, private id: number) {}
}

// Generics
function identity<T>(value: T): T {
  return value;
}
```

### Recommended Practices
- Always enable `strict` mode in `tsconfig.json`.
- Prefer `readonly` and `private` for encapsulation.
- Avoid `any` — use proper types or generics.

---

## 🏗️ 2. Angular Project Anatomy

When using the **standalone** approach:
```bash
ng new angular-fundamentals --standalone
```

Key files:
```
/src
 ┣ app/
 ┃ ┣ app.component.ts
 ┃ ┣ app.component.html
 ┃ ┗ app.config.ts
 ┣ main.ts
 ┗ index.html
```

### `main.ts`
```typescript
import { bootstrapApplication } from '@angular/platform-browser';
import { AppComponent } from './app/app.component';
import { appConfig } from './app/app.config';

bootstrapApplication(AppComponent, appConfig)
  .catch(err => console.error(err));
```

---

## 🧩 3. Standalone Components

Angular 17+ embraces **standalone components**, eliminating `NgModule` boilerplate.

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-hello',
  standalone: true,
  template: `<h1>Hello {{ name }}</h1>`
})
export class HelloComponent {
  name = 'Angular';
}
```

To use inside another component:
```typescript
@Component({
  selector: 'app-root',
  standalone: true,
  imports: [HelloComponent],
  template: `<app-hello></app-hello>`
})
export class AppComponent {}
```

---

## 🧵 4. Templates & Data Binding

### String Interpolation
```html
<h1>Welcome, {{ username }}</h1>
```

### Property Binding
```html
<img [src]="imageUrl" [alt]="username">
```

### Event Binding
```html
<button (click)="onClick()">Click Me</button>
```

### Two-Way Binding
```html
<input [(ngModel)]="username">
```
> Requires `FormsModule` import in the standalone config.

---

## ⚙️ 5. Directives (`*ngIf`, `*ngFor`, `[ngClass]`, `[ngStyle]`)

### Conditional Rendering
```html
<p *ngIf="isVisible">Visible content</p>
```

### Loops
```html
<li *ngFor="let user of users; trackBy: trackById">{{ user.name }}</li>
```

### Dynamic Classes & Styles
```html
<div [ngClass]="{ active: isActive }" [ngStyle]="{ color: 'red' }"></div>
```

---

## 🧮 6. Pipes

Pipes transform data in templates.
```html
<p>{{ today | date:'short' }}</p>
<p>{{ price | currency:'USD' }}</p>
```

### Custom Pipe Example
```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({ name: 'capitalize', standalone: true })
export class CapitalizePipe implements PipeTransform {
  transform(value: string): string {
    return value.charAt(0).toUpperCase() + value.slice(1);
  }
}
```

---

## 🔁 7. Change Detection Basics

Angular’s change detection uses the **Zone.js** mechanism.

### Default Strategy
Runs automatically whenever async events occur (e.g., click, HTTP, timeout).

### OnPush Strategy
Used for performance optimization:
```typescript
@Component({
  selector: 'app-optimized',
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `{{ data }}`
})
export class OptimizedComponent {
  @Input() data!: string;
}
```
> Only triggers when `@Input()` changes or signals emit.

---

## 🧠 Review Checklist
- [ ] Understand standalone bootstrap flow (`bootstrapApplication`).
- [ ] Master `@Component` properties and metadata.
- [ ] Use `*ngIf`/`*ngFor` correctly.
- [ ] Implement custom pipes.
- [ ] Know when to use `OnPush` change detection.

---

## 📚 Next: Part II – Components & Templates Deep Dive
Covers **Input/Output bindings**, **view encapsulation**, **content projection**, **lifecycle hooks**, **host listeners**, and **accessibility improvements**.

