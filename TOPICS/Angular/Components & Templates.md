# 🅰️ Angular Mastery Series – Part II: Components & Templates Deep Dive

---

## 🎯 Objective
In this part, we’ll explore **how Angular components interact**, focusing on:
- Input/Output bindings
- Lifecycle hooks
- View encapsulation
- Content projection (`ng-content`)
- Template references
- Host binding/listeners
- Accessibility and best practices

---

## 🧩 1. Input and Output Bindings

### Parent-to-Child Communication (`@Input`)
```typescript
import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-user-card',
  standalone: true,
  template: `<h3>{{ user.name }}</h3>`
})
export class UserCardComponent {
  @Input() user!: { name: string };
}
```

### Child-to-Parent Communication (`@Output`)
```typescript
import { Component, EventEmitter, Output } from '@angular/core';

@Component({
  selector: 'app-like-button',
  standalone: true,
  template: `<button (click)="liked.emit()">Like</button>`
})
export class LikeButtonComponent {
  @Output() liked = new EventEmitter<void>();
}
```

### Usage Example
```html
<app-like-button (liked)="onLiked()"></app-like-button>
```

---

## 🔁 2. Lifecycle Hooks
Angular components have a lifecycle that allows you to react to initialization, updates, and destruction.

```typescript
import { Component, OnInit, OnDestroy } from '@angular/core';

@Component({
  selector: 'app-logger',
  standalone: true,
  template: `<p>Logger Active</p>`
})
export class LoggerComponent implements OnInit, OnDestroy {
  ngOnInit() {
    console.log('Logger initialized');
  }
  ngOnDestroy() {
    console.log('Logger destroyed');
  }
}
```

### Common Hooks
| Hook | Purpose |
|------|----------|
| `ngOnInit` | Initialization after inputs are set |
| `ngOnChanges` | Respond to input property changes |
| `ngAfterViewInit` | DOM and child views ready |
| `ngOnDestroy` | Cleanup before component removal |

---

## 🧱 3. View Encapsulation
Controls how styles from one component affect others.

```typescript
@Component({
  selector: 'app-box',
  standalone: true,
  encapsulation: ViewEncapsulation.Emulated, // Default
  template: `<div class="box">Box</div>`,
  styles: [`.box { color: blue; }`]
})
export class BoxComponent {}
```

### Options
| Mode | Description |
|------|--------------|
| `Emulated` | Default. Scoped styles per component |
| `ShadowDom` | Uses native Shadow DOM |
| `None` | Global styles |

---

## 🧵 4. Content Projection (`ng-content`)
Allows passing custom markup into a component.

```typescript
@Component({
  selector: 'app-card',
  standalone: true,
  template: `
    <div class="card">
      <ng-content></ng-content>
    </div>
  `,
  styles: [`.card { padding: 1rem; border: 1px solid #ccc; border-radius: 8px; }`]
})
export class CardComponent {}
```

Usage:
```html
<app-card>
  <h2>Inside projected content</h2>
</app-card>
```

### Multiple Slots Example
```html
<div class="dialog">
  <ng-content select="[header]"></ng-content>
  <ng-content select="[body]"></ng-content>
</div>
```

---

## 🔍 5. Template References & ViewChild
Template variables allow referencing DOM elements or components.

```html
<input #nameInput type="text">
<button (click)="logName(nameInput.value)">Log</button>
```

In component:
```typescript
logName(value: string) {
  console.log('Name:', value);
}
```

### Access via `@ViewChild`
```typescript
@ViewChild('nameInput') nameInput!: ElementRef<HTMLInputElement>;

ngAfterViewInit() {
  console.log(this.nameInput.nativeElement.value);
}
```

---

## ⚡ 6. Host Binding & Host Listener

```typescript
import { Component, HostBinding, HostListener } from '@angular/core';

@Component({
  selector: 'app-hover-box',
  standalone: true,
  template: `<p>Hover me!</p>`
})
export class HoverBoxComponent {
  @HostBinding('class.hovered') isHovered = false;

  @HostListener('mouseenter') onEnter() {
    this.isHovered = true;
  }

  @HostListener('mouseleave') onLeave() {
    this.isHovered = false;
  }
}
```

---

## ♿ 7. Accessibility (a11y) Basics

### Key Tips
- Use **semantic HTML** (`<button>`, `<label>`, `<input>`)
- Include `aria-*` attributes for dynamic components
- Manage focus with `@ViewChild` or `cdkFocusInitial`
- Test using **Chrome Lighthouse → Accessibility tab**

Example:
```html
<button aria-label="Close dialog" (click)="close()">✖</button>
```

---

## 🧠 Review Checklist
- [ ] Use `@Input`/`@Output` for component communication.
- [ ] Implement lifecycle hooks for controlled logic.
- [ ] Apply `ViewEncapsulation` appropriately.
- [ ] Utilize `ng-content` for flexible layouts.
- [ ] Access elements with `@ViewChild` safely.
- [ ] Use `@HostBinding` and `@HostListener` for DOM event handling.
- [ ] Follow accessibility best practices.

---

## 📚 Next: Part III – Dependency Injection & Services
Covers **DI system**, **providers**, **hierarchical injectors**, **tokens**, and the new **`inject()` API** for standalone setups.