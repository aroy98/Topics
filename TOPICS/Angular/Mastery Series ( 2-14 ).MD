# Angular Mastery Series — Questions, Explanations, and Examples (Legacy Module-Based Angular)

This document covers **Angular 2–14 (pre-Standalone)** structure using **NgModules**, **RxJS**, **Reactive Forms**, **Routing**, **Services**, and **Testing**. Each section includes **questions**, **explanations**, and **examples**.

---

## 🧩 Part I — Fundamentals (Modules, Components, CLI)

### **Question 1:** How does the Angular architecture work?

**Explanation:** Angular follows a modular architecture with **Modules**, **Components**, **Templates**, **Services**, and **Dependency Injection**.

**Example — App Module**

```ts
// app.module.ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AppComponent } from './app.component';

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

**Example — Component**

```ts
// app.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = 'Angular Legacy Mastery';
}
```

---

## 🔁 Part II — Data Binding, Directives, and Pipes

### **Question 2:** What types of data binding exist in Angular?

**Explanation:**

* **Interpolation**: `{{ value }}`
* **Property Binding**: `[property]="value"`
* **Event Binding**: `(event)="handler()"`
* **Two-way Binding**: `[(ngModel)]="property"`

**Example:**

```html
<input [(ngModel)]="name" />
<p>Hello {{ name }}</p>
```

**Directives:** Built-in (`ngIf`, `ngFor`, `ngClass`) or custom (`@Directive`).
**Pipes:** Transform data in templates (e.g., `| date`, `| uppercase`).

**Example — Custom Pipe**

```ts
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({ name: 'reverse' })
export class ReversePipe implements PipeTransform {
  transform(value: string): string {
    return value.split('').reverse().join('');
  }
}
```

---

## 🧭 Part III — Routing & Navigation

### **Question 3:** How to configure and navigate between routes?

**Explanation:** Use `RouterModule` and `RouterLink` for SPA routing.

**Example — App Routing Module**

```ts
// app-routing.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { HomeComponent } from './home/home.component';
import { ProductsComponent } from './products/products.component';

const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'products', component: ProductsComponent },
  { path: '**', redirectTo: '' }
];

@NgModule({ imports: [RouterModule.forRoot(routes)], exports: [RouterModule] })
export class AppRoutingModule {}
```

---

## 🌐 Part IV — Services, Dependency Injection, and HTTP

### **Question 4:** How does dependency injection work in Angular?

**Explanation:** Angular injects services as singletons within modules or components.

**Example — Service with HTTP**

```ts
// product.service.ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable({ providedIn: 'root' })
export class ProductService {
  constructor(private http: HttpClient) {}
  getProducts(): Observable<any[]> {
    return this.http.get<any[]>('https://api.example.com/products');
  }
}
```

**Example — Consuming in Component**

```ts
// products.component.ts
import { Component, OnInit } from '@angular/core';
import { ProductService } from './product.service';

@Component({ selector: 'app-products', templateUrl: './products.component.html' })
export class ProductsComponent implements OnInit {
  products: any[] = [];
  constructor(private ps: ProductService) {}
  ngOnInit() { this.ps.getProducts().subscribe(data => this.products = data); }
}
```

---

## 🧰 Part V — Forms (Template-driven & Reactive)

### **Question 5:** Difference between Template-driven and Reactive Forms?

**Explanation:**

* **Template-driven**: Simple forms using `ngModel`.
* **Reactive**: Model-driven, with `FormGroup`, `FormControl`, and validators.

**Example — Reactive Form**

```ts
// user-form.component.ts
import { Component } from '@angular/core';
import { FormBuilder, Validators } from '@angular/forms';

@Component({ selector: 'app-user-form', templateUrl: './user-form.component.html' })
export class UserFormComponent {
  form = this.fb.group({ name: ['', Validators.required], email: ['', [Validators.email]] });
  constructor(private fb: FormBuilder) {}
  save() { console.log(this.form.value); }
}
```

---

## 🧩 Part VI — RxJS, Observables, and Async Patterns

### **Question 6:** What is RxJS and why is it important?

**Explanation:** RxJS handles asynchronous streams (HTTP, events). Core concepts: **Observable**, **Observer**, **Operators**.

**Example — Using RxJS Operators**

```ts
this.http.get('/api/data').pipe(
  map(res => res.items),
  filter(items => items.length > 0),
  catchError(err => of([]))
).subscribe(console.log);
```

---

## 🧪 Part VII — Testing (Jasmine/Karma)

### **Question 7:** How to test components and services?

**Explanation:** Use **TestBed** for component creation and **HttpTestingController** for service tests.

**Example — Component Test**

```ts
import { TestBed } from '@angular/core/testing';
import { AppComponent } from './app.component';

describe('AppComponent', () => {
  beforeEach(async () => {
    await TestBed.configureTestingModule({ declarations: [AppComponent] }).compileComponents();
  });

  it('should create', () => {
    const fixture = TestBed.createComponent(AppComponent);
    const app = fixture.componentInstance;
    expect(app).toBeTruthy();
  });
});
```

**Example — Service Test**

```ts
import { HttpClientTestingModule, HttpTestingController } from '@angular/common/http/testing';
import { TestBed } from '@angular/core/testing';
import { ProductService } from './product.service';

describe('ProductService', () => {
  let service: ProductService;
  let httpMock: HttpTestingController;

  beforeEach(() => {
    TestBed.configureTestingModule({ imports: [HttpClientTestingModule] });
    service = TestBed.inject(ProductService);
    httpMock = TestBed.inject(HttpTestingController);
  });

  it('fetches products', () => {
    service.getProducts().subscribe(res => expect(res.length).toBeGreaterThan(0));
    const req = httpMock.expectOne('https://api.example.com/products');
    req.flush([{ id: 1 }]);
  });
});
```

---

## 🚀 Part VIII — Optimization & Deployment

### **Question 8:** How to optimize Angular apps for production?

**Explanation:**

* Use `ng build --prod`.
* Lazy-load modules.
* Use `ChangeDetectionStrategy.OnPush`.
* Preload assets & enable compression.

**Example — Lazy Loading**

```ts
const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'users', loadChildren: () => import('./users/users.module').then(m => m.UsersModule) }
];
```

### **Question 9:** How to deploy Angular apps?

**Explanation:** Build produces static files (`dist/`). Host on Nginx, Apache, S3, or Firebase.

**Example — Nginx Configuration**

```nginx
server {
  listen 80;
  root /usr/share/nginx/html;
  index index.html;
  location / {
    try_files $uri $uri/ /index.html;
  }
}
```

---

## ✅ Summary Checklist

| Area             | Focus                                     |
| ---------------- | ----------------------------------------- |
| **Architecture** | NgModules, Components, Services           |
| **HTTP**         | HttpClient, Observables, Interceptors     |
| **State**        | RxJS, AsyncPipe, Subject, BehaviorSubject |
| **Forms**        | Template-driven, Reactive Forms           |
| **Routing**      | Modules, Guards, Lazy Loading             |
| **Testing**      | TestBed, Karma, HttpTestingController     |
| **Deployment**   | Build Optimization, Lazy Loading, Nginx   |

---

**End of Document — Angular Mastery Series (Legacy Module-Based)**
