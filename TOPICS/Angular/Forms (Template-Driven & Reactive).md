# 🅰️ Angular Mastery Series – Part V: Forms (Template-Driven & Reactive)

---

## 🎯 Objective
Master **Template-Driven** and **Reactive Forms** in Angular. Build complex forms with **validation (sync & async)**, **FormArray** patterns, **typed forms**, **custom validators**, and **ControlValueAccessor (CVA)** for reusable, enterprise-grade inputs.

---

## 🧭 Choosing a Strategy
| Use Case | Template-Driven | Reactive |
|---|---|---|
| Simple forms, low logic | ✅ | — |
| Dynamic fields, complex validation | — | ✅ |
| Testability & scalability | — | ✅ |
| Strict typing | — | ✅ |

> Rule of thumb: **Prefer Reactive Forms** for anything beyond trivial forms.

---

## 🧱 1) Template-Driven Forms (Quick Start)

```ts
// app.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [FormsModule],
  template: `
    <form #f="ngForm" (ngSubmit)="submit(f.value)">
      <input name="email" ngModel required email>
      <input name="age" ngModel min="18" type="number">
      <button type="submit" [disabled]="f.invalid">Submit</button>
      <pre>{{ f.value | json }}</pre>
    </form>
  `
})
export class AppComponent {
  submit(val: unknown) { console.log(val); }
}
```

**Pros:** minimal boilerplate. **Cons:** limited control, harder to test.

---

## 🧪 2) Reactive Forms – Typed & Composable

```ts
// user-form.component.ts
import { Component } from '@angular/core';
import { ReactiveFormsModule, NonNullableFormBuilder, Validators } from '@angular/forms';

interface UserForm {
  name: string;
  email: string;
  age: number | null;
  skills: string[];
}

@Component({
  selector: 'app-user-form',
  standalone: true,
  imports: [ReactiveFormsModule],
  template: `
    <form [formGroup]="form" (ngSubmit)="save()">
      <input formControlName="name" placeholder="Name">
      <input formControlName="email" placeholder="Email">
      <input formControlName="age" type="number" placeholder="Age">

      <div formArrayName="skills">
        <div *ngFor="let ctrl of skills.controls; let i = index">
          <input [formControlName]="i" placeholder="Skill">
          <button type="button" (click)="removeSkill(i)">Remove</button>
        </div>
        <button type="button" (click)="addSkill()">Add Skill</button>
      </div>

      <button type="submit" [disabled]="form.invalid">Save</button>
      <pre>{{ form.value | json }}</pre>
    </form>
  `
})
export class UserFormComponent {
  private fb = new NonNullableFormBuilder();

  form = this.fb.group<UserForm>({
    name: this.fb.control('', { validators: [Validators.required, Validators.minLength(2)] }),
    email: this.fb.control('', { validators: [Validators.required, Validators.email] }),
    age: this.fb.control<number | null>(null),
    skills: this.fb.control<string[]>([])
  });

  get skillsArray() { return this.form.controls.skills; }
  get skills() { return this.form.controls.skills as any; }

  addSkill() { this.skillsArray.setValue([...(this.skillsArray.value ?? []), '']); }
  removeSkill(i: number) {
    const next = [...(this.skillsArray.value ?? [])];
    next.splice(i, 1);
    this.skillsArray.setValue(next);
  }

  save() { console.log(this.form.getRawValue()); }
}
```

> Use `NonNullableFormBuilder` to avoid `null` unless you **intend** nullable values.

---

## 🧰 3) Reactive Forms with `FormArray` (Dynamic Controls)

Prefer `FormArray` when you actually need separate controls per item (validators, status, errors).

```ts
import { FormArray, FormControl, FormGroup } from '@angular/forms';

form = new FormGroup({
  name: new FormControl('', { nonNullable: true, validators: [Validators.required] }),
  emails: new FormArray<FormControl<string>>([])
});

get emails() { return this.form.controls.emails; }
addEmail() { this.emails.push(new FormControl('', { nonNullable: true, validators: [Validators.email] })); }
removeEmail(i: number) { this.emails.removeAt(i); }
```

Template:
```html
<div formArrayName="emails">
  <div *ngFor="let c of emails.controls; let i = index">
    <input [formControlName]="i" placeholder="Email">
  </div>
  <button type="button" (click)="addEmail()">Add email</button>
</div>
```

---

## ✅ 4) Validation (Sync & Async)

### Sync Validators
```ts
import { Validators } from '@angular/forms';
const ctrl = new FormControl('', [Validators.required, Validators.minLength(3)]);
```

### Custom Sync Validator
```ts
import { AbstractControl, ValidationErrors } from '@angular/forms';
export function forbiddenName(names: string[]) {
  return (control: AbstractControl): ValidationErrors | null =>
    names.includes(String(control.value).toLowerCase()) ? { forbiddenName: true } : null;
}
```

### Async Validator (e.g., unique username)
```ts
import { AsyncValidatorFn } from '@angular/forms';
import { map, delay, of } from 'rxjs';

export const uniqueUser: AsyncValidatorFn = (control) => {
  const existing = ['alice', 'bob'];
  return of(existing.includes(control.value)).pipe(
    delay(500),
    map(isTaken => (isTaken ? { userTaken: true } : null))
  );
};
```

### Displaying Errors (Helper)
```html
<input formControlName="email">
<div class="error" *ngIf="form.controls.email.touched && form.controls.email.errors as e">
  <span *ngIf="e['required']">Email is required</span>
  <span *ngIf="e['email']">Invalid email</span>
</div>
```

---

## 🔌 5) ControlValueAccessor (CVA) – Reusable Inputs
Create custom form controls that plug into Angular forms like native inputs.

```ts
import { Component, forwardRef } from '@angular/core';
import { ControlValueAccessor, NG_VALUE_ACCESSOR } from '@angular/forms';

@Component({
  selector: 'app-rating',
  standalone: true,
  template: `
    <button *ngFor="let s of stars; index as i" (click)="set(i+1)" [class.active]="i < value">★</button>
  `,
  providers: [{ provide: NG_VALUE_ACCESSOR, useExisting: forwardRef(() => RatingComponent), multi: true }]
})
export class RatingComponent implements ControlValueAccessor {
  value = 0;
  stars = Array(5);

  private onChange: (v: number) => void = () => {};
  private onTouched: () => void = () => {};

  writeValue(v: number): void { this.value = v ?? 0; }
  registerOnChange(fn: any): void { this.onChange = fn; }
  registerOnTouched(fn: any): void { this.onTouched = fn; }
  setDisabledState?(isDisabled: boolean): void { /* handle */ }

  set(v: number) { this.value = v; this.onChange(v); this.onTouched(); }
}
```

Usage:
```html
<form [formGroup]="form">
  <app-rating formControlName="rating"></app-rating>
</form>
```

---

## 🧷 6) Form Performance & UX Tips
- Use `OnPush` components and **`trackBy`** in repeated controls.
- Debounce value changes for expensive validations.
- Prefer **reactive** pattern for dynamic forms; avoid heavy `[ngModel]` in large templates.
- Use `updateOn: 'blur' | 'submit'` to reduce validation thrash.
- Keep server errors in a separate `FormGroup` control or map them onto controls via `setErrors`.

```ts
new FormControl('', { validators: [Validators.required], updateOn: 'blur' });
```

---

## 🧷 7) Interop with Signals (optional)

```ts
import { toSignal } from '@angular/core/rxjs-interop';

valueSig = toSignal(this.form.valueChanges, { initialValue: this.form.value });
```

Use computed signals to derive derived UI states from form values.

---

## 🔒 8) Security & Sanitization
- Never trust client-side validation alone; **re-validate on server**.
- Sanitize or validate rich text/HTML inputs; use `DomSanitizer` cautiously.
- Prevent over-posting: bind only necessary fields on server.

---

## 🧠 Review Checklist
- [ ] Pick Reactive Forms for dynamic/complex UIs.
- [ ] Use **typed forms** and `NonNullableFormBuilder`.
- [ ] Implement sync/async validators with clear UX.
- [ ] Use `FormArray` for dynamic lists of controls.
- [ ] Create reusable inputs with **CVA**.
- [ ] Tune UX with `updateOn`, debouncing, and OnPush.

---

## 📚 Next: Part VI – RxJS & State Management (NgRx)
We’ll build reactive data flows with RxJS, connect components via facades, and introduce **NgRx Store/Effects/Entity**, plus **signals interop**, selectors, and testing patterns.

