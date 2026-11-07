### Complete Angular Tutorial: Beginner to Advanced

This tutorial will teach you modern Angular with TypeScript, starting from the basics and progressing to advanced concepts, including REST API integration with Spring Boot, authentication, RxJS, and deployment.

Since you're learning Java and Spring Boot, we'll use a familiar layered structure: components → services → HTTP client → Spring Boot API.

### 1\. What is Angular?

Angular is a frontend framework used to build single-page applications (SPAs), dashboards, admin panels, e-commerce websites, and enterprise applications.

![JavaScript Libraries — Boosting Productivity and Enhancing User Experience](https://images.openai.com/static-rsc-4/KN-nU7ZCmp9_tEIrqhZQbDeqVA_MhvEgybPDrnfdxPpyF5UK3MMK-MWFi76LaCoHeDdvRXzdaY1L0RLncxt428eEDUkgMjBg407-ItckTTldUvmFkXDFUBkHwn7XNXOS-Brc3-CjCBEi1ukRNwf2m5PtffEz5J55OdFvdB5mWtOs5ziz2MrPsf90-ib69oHM?purpose=fullsize)

Angular

Frontend framework, components, routing, forms

![TypeScript Basic](https://images.openai.com/static-rsc-4/AFHKyDj05r2SKqF-Qk2pT8RW_XOmDd56wtSjHDjoEhkti4VjF-qqz-BmkY1qqbBNY8t_DBtEzjlezI5ZMyLtTAtOVjoPP9-nh_hVTG71o4NGTYF2j_3d04LuUiHTm53mZAOh6oX5KpPsQiDODCtP43TestaeV8bRi2iLSv-fDMbz-02HRw2mhdLnVCmNOmN-?purpose=fullsize)

TypeScript

JavaScript with static types

### How Angular works with Spring Boot

Angular frontend

Components · HTML templates · Forms

HTTP / REST API

JSON · GET · POST · PUT · DELETE

Spring Boot backend

Controller → Service → Repository → PostgreSQL

Angular handles the user interface. Spring Boot handles backend business logic, database operations, and server-side security.

### 2\. Learning roadmap

Follow these topics in order. You don't need to learn everything at once.

Level 1 — Fundamentals

HTML, CSS, TypeScript, Angular CLI, project structure, components, templates, interpolation, property binding, event binding, signals, control flow, and directives.

Beginner

Level 2 — Application development

Input/output, reusable components, dependency injection, services, routing, template-driven forms, reactive forms, and validation.

Intermediate

Level 3 — API integration

HttpClient, Observables, RxJS operators, CRUD, loading states, error handling, interceptors, and Spring Boot integration.

Full stack

Level 4 — Advanced Angular

Authentication, route guards, lazy loading, advanced RxJS, state management, signals, performance, testing, SSR, accessibility, and deployment.

Advanced

### 3\. Install Angular on Fedora/Linux

Angular requires Node.js and npm. First, check whether they are installed.

```bash
node -v
npm -v
```

If either command is missing, install a compatible Node.js version using a version manager such as `nvm`, following the [official Node.js download page](https://nodejs.org/en/download?utm_source=chatgpt.com).

Install the Angular CLI:

```bash
npm install -g @angular/cli
ng version
```

Create your first project:

```bash
ng new angular-tutorial --routing --style=css
cd angular-tutorial
ng serve --open
```

If `--open` doesn't open your browser, visit `http://localhost:4200`.

These commands use Angular's standalone-oriented project setup. For the exact Node.js and TypeScript requirements, check the [official Angular installation guide](https://angular.dev/installation?utm_source=chatgpt.com). Angular +1

### 4\. Understand the project structure

Your generated project will contain files similar to these:

```text
angular-tutorial/
├── public/
├── src/
│   ├── app/
│   │   ├── app.ts
│   │   ├── app.html
│   │   ├── app.css
│   │   └── app.routes.ts
│   ├── index.html
│   ├── main.ts
│   └── styles.css
├── angular.json
├── package.json
└── tsconfig.json
```

Some CLI versions use different filenames, such as `app.component.ts` instead of `app.ts`. The concepts remain the same.

| File          | Purpose                               |
| ------------- | ------------------------------------- |
| app.ts        | Component logic written in TypeScript |
| app.html      | HTML template displayed by Angular    |
| app.css       | Component-specific styling            |
| app.routes.ts | URL-to-component mappings             |
| main.ts       | Starts the Angular application        |
| package.json  | Dependencies and npm commands         |
| angular.json  | Angular CLI build configuration       |

### 5\. TypeScript fundamentals

Angular uses TypeScript, so learn these concepts before going deeper.

```typescript
// Variables and types
let name: string = 'Pavan';
let age: number = 22;
let isStudent: boolean = true;

// Function
function add(a: number, b: number): number {
  return a + b;
}

// Class
class User {
  constructor(
    public name: string,
    public age: number
  ) {}
}

// Array of objects
const users: User[] = [
  new User('Amit', 22),
  new User('Ravi', 24)
];

console.log(add(10, 20));
console.log(users);
```

Important concepts to learn:

* `let`, `const`, types and type inference

* Functions, arrow functions and interfaces

* Classes and constructors

* Arrays, objects and generics

* `import` and `export`

* Promises and `async/await`

* Optional properties and union types

For example, an interface describes the shape of data received from a Spring Boot API:

```typescript
export interface Product {
  id: number;
  name: string;
  price: number;
}
```

This is similar to defining a Java DTO, although a TypeScript interface is a compile-time type and doesn't perform runtime validation.

### 6\. Angular components

A component is a reusable piece of the user interface. It usually contains a TypeScript class, an HTML template and CSS styles. Angular +1

Generate a component:

```bash
ng generate component counter
```

A simple component can look like this:

`counter.ts`

```typescript
import { Component, signal } from '@angular/core';

@Component({
  selector: 'app-counter',
  template: `
    <h2>Counter: {{ count() }}</h2>
    <button (click)="increment()">Increment</button>
  `
})
export class Counter {
  count = signal(0);

  increment() {
    this.count.update(value => value + 1);
  }
}
```

To display it in another standalone component, import `Counter` in that component's `imports` array and use its selector:

```html
<app-counter />
```

The example uses a signal, Angular's reactive state mechanism. When `count` changes, Angular updates the displayed value.

### Key component concepts

* `@Component` — configures a component.

* `selector` — identifies the component in a template.

* `template` / `templateUrl` — defines its HTML.

* `styleUrl` / `styleUrls` — defines its CSS.

* Component class — holds data and methods.

### 7\. Data binding

Data binding connects your TypeScript component with its HTML template.

| Type             | Syntax     | Example              |
| ---------------- | ---------- | -------------------- |
| Interpolation    | {{ }}      | {{ title }}          |
| Property binding | [property] | [disabled]="loading" |
| Event binding    | (event)    | (click)="save()"     |
| Two-way binding  | [(...)]    | [(ngModel)]="name"   |

Example:

```typescript
import { Component, signal } from '@angular/core';

@Component({
  selector: 'app-demo',
  template: `
    <h1>{{ title() }}</h1>

    <button
      [disabled]="count() >= 5"
      (click)="increment()">
      Count: {{ count() }}
    </button>
  `
})
export class Demo {
  title = signal('Data Binding');
  count = signal(0);

  increment() {
    this.count.update(value => value + 1);
  }
}
```

Here, `[disabled]` controls the button property, while `(click)` invokes a method.

### 8\. Template control flow

Modern Angular provides built-in `@if`, `@for` and `@switch` syntax.

```typescript
users = signal([
  { id: 1, name: 'Amit' },
  { id: 2, name: 'Ravi' },
  { id: 3, name: 'Priya' }
]);

showUsers = signal(true);
```

```html
@if (showUsers()) {
  <ul>
    @for (user of users(); track user.id) {
      <li>{{ user.name }}</li>
    } @empty {
      <li>No users found.</li>
    }
  </ul>
} @else {
  <p>Users are hidden.</p>
}

<button (click)="showUsers.update(v => !v)">
  Toggle users
</button>
```

* `@if` conditionally renders content.

* `@for` repeats content for each item.

* `track user.id` helps Angular associate rendered items with their data.

* `@empty` displays a message when the list is empty.

* `@switch` handles multiple possible values.

You can use these without the older `*ngIf` and `*ngFor` directives. Angular +1

### 9\. Services and dependency injection

In Spring, you might create a service annotated with `@Service`. Angular has a similar concept: a class that contains reusable application logic.

Generate a service:

```bash
ng generate service services/product
```

`product.ts`

```typescript
import { Injectable, signal } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class ProductService {
  products = signal([
    { id: 1, name: 'Keyboard', price: 800 },
    { id: 2, name: 'Mouse', price: 400 }
  ]);

  getProducts() {
    return this.products();
  }
}
```

Use the service in a component:

```typescript
import { Component, inject } from '@angular/core';
import { ProductService } from './services/product';

@Component({
  selector: 'app-products',
  template: `
    @for (product of productService.getProducts(); track product.id) {
      <p>{{ product.name }} — ₹{{ product.price }}</p>
    }
  `
})
export class Products {
  productService = inject(ProductService);
}
```

`providedIn: 'root'` makes the service available through the application's dependency injection system.

Think of the relationship this way:

| Spring Boot           | Angular                                                        |
| --------------------- | -------------------------------------------------------------- |
| @Service              | @Injectable service                                            |
| Dependency injection  | Dependency injection                                           |
| Constructor injection | inject() or constructor injection                              |
| Controller            | Often a component for UI handling, but not an exact equivalent |
| DTO                   | TypeScript interface or type                                   |

### 10\. Routing

Routing lets users navigate between pages without reloading the whole application. Angular

Define routes in `app.routes.ts`:

```typescript
import { Routes } from '@angular/router';
import { Home } from './home';
import { Products } from './products';

export const routes: Routes = [
  { path: '', component: Home },
  { path: 'products', component: Products },
  { path: '**', redirectTo: '' }
];
```

Use navigation links in your main template:

```html
<nav>
  <a routerLink="/">Home</a>
  <a routerLink="/products">Products</a>
</nav>

<router-outlet />
```

Import `RouterLink` and `RouterOutlet` into the component that uses these template elements.

The generated project normally configures `provideRouter(routes)` in `app.config.ts`.

Advanced routing topics:

* Route parameters, such as `/products/10`

* Query parameters, such as `?category=books`

* Nested routes

* Lazy loading

* Route guards

* Resolvers

* Authentication-aware navigation

### 11\. Forms and validation

Angular has two traditional form approaches.

| Approach              | Best use                              |
| --------------------- | ------------------------------------- |
| Template-driven forms | Simple forms                          |
| Reactive forms        | Complex forms and explicit validation |

For a registration page, reactive forms are a good starting point.

```typescript
import { Component, inject } from '@angular/core';
import {
  FormBuilder,
  ReactiveFormsModule,
  Validators
} from '@angular/forms';

@Component({
  selector: 'app-register',
  imports: [ReactiveFormsModule],
  template: `
    <form [formGroup]="form" (ngSubmit)="register()">
      <input formControlName="email" placeholder="Email">

      @if (form.controls.email.touched &&
           form.controls.email.invalid) {
        <p>Enter a valid email.</p>
      }

      <input
        type="password"
        formControlName="password"
        placeholder="Password">

      <button [disabled]="form.invalid">Register</button>
    </form>
  `
})
export class Register {
  private fb = inject(FormBuilder);

  form = this.fb.nonNullable.group({
    email: ['', [Validators.required, Validators.email]],
    password: ['', [Validators.required, Validators.minLength(8)]]
  });

  register() {
    if (this.form.invalid) return;

    console.log(this.form.getRawValue());
  }
}
```

This example only validates the form and logs its values. The next step is sending those values to a backend API. Client-side validation improves the user experience, but Spring Boot must also validate the submitted data.

Note: Newer Angular releases also offer Signal Forms; learn the traditional reactive approach first if you're starting out, then explore the newer API when you are comfortable with form state and validation. GitHub +1

### 12\. HTTP and Spring Boot REST API integration

This is one of the most important sections for full-stack development.

First, configure Angular's HTTP client in `app.config.ts`:

```typescript
import { ApplicationConfig } from '@angular/core';
import { provideHttpClient } from '@angular/common/http';

export const appConfig: ApplicationConfig = {
  providers: [
    provideHttpClient()
  ]
};
```

Create a service for your Spring Boot API.

`product.ts`

```typescript
import { Injectable, inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Product } from '../models/product';

@Injectable({
  providedIn: 'root'
})
export class ProductService {
  private http = inject(HttpClient);

  private apiUrl = 'http://localhost:8080/api/products';

  getAll() {
    return this.http.get<Product[]>(this.apiUrl);
  }

  getById(id: number) {
    return this.http.get<Product>(`${this.apiUrl}/${id}`);
  }

  create(product: Omit<Product, 'id'>) {
    return this.http.post<Product>(this.apiUrl, product);
  }

  update(id: number, product: Product) {
    return this.http.put<Product>(`${this.apiUrl}/${id}`, product);
  }

  delete(id: number) {
    return this.http.delete<void>(`${this.apiUrl}/${id}`);
  }
}
```

The `Product` interface should match the JSON returned by your Spring Boot API. Adjust the methods and URL to match your actual controller.

### Display API data

```typescript
import { Component, inject, signal } from '@angular/core';
import { ProductService } from './services/product';
import { Product } from './models/product';

@Component({
  selector: 'app-products',
  template: `
    <h2>Products</h2>

    @if (loading()) {
      <p>Loading products...</p>
    } @else if (error()) {
      <p>{{ error() }}</p>
    } @else {
      @for (product of products(); track product.id) {
        <p>{{ product.name }} — ₹{{ product.price }}</p>
      }
    }

    <button (click)="loadProducts()">Reload</button>
  `
})
export class Products {
  private service = inject(ProductService);

  products = signal<Product[]>([]);
  loading = signal(false);
  error = signal('');

  loadProducts() {
    this.loading.set(true);
    this.error.set('');

    this.service.getAll().subscribe({
      next: data => {
        this.products.set(data);
        this.loading.set(false);
      },
      error: () => {
        this.error.set('Could not load products.');
        this.loading.set(false);
      }
    });
  }
}
```

For this to load automatically when the component opens, call `loadProducts()` from an appropriate initialization hook or use a reactive data-fetching approach.

Important: `HttpClient` returns an Observable. Calling `.subscribe()` starts the request, and its callbacks handle the response or error.

To make this work locally, your Spring Boot application must be running on port `8080`, expose the matching endpoint, and allow requests from the Angular development origin through appropriate CORS configuration.

### 13\. RxJS and asynchronous programming

Angular uses RxJS extensively, especially for HTTP requests, event streams, and reactive search.

Learn these operators:

| Operator             | Purpose                                      |
| -------------------- | -------------------------------------------- |
| map                  | Transform emitted values                     |
| filter               | Keep matching values                         |
| tap                  | Perform side effects                         |
| catchError           | Handle errors                                |
| switchMap            | Switch to a new Observable                   |
| debounceTime         | Wait for input to settle                     |
| distinctUntilChanged | Skip consecutive duplicate values            |
| forkJoin             | Combine finite Observables when all complete |

Example of a search field that avoids sending a request on every keystroke:

```typescript
import { Component, inject } from '@angular/core';
import { FormControl, ReactiveFormsModule } from '@angular/forms';
import { HttpClient } from '@angular/common/http';
import { debounceTime, distinctUntilChanged, switchMap } from 'rxjs';

@Component({
  selector: 'app-search',
  imports: [ReactiveFormsModule],
  template: `
    <input [formControl]="search" placeholder="Search products">

    @for (product of results; track product.id) {
      <p>{{ product.name }}</p>
    }
  `
})
export class Search {
  private http = inject(HttpClient);

  search = new FormControl('', { nonNullable: true });
  results: { id: number; name: string }[] = [];

  constructor() {
    this.search.valueChanges.pipe(
      debounceTime(300),
      distinctUntilChanged(),
      switchMap(query =>
        this.http.get<{ id: number; name: string }[]>(
          '/api/products',
          { params: { search: query } }
        )
      )
    ).subscribe(data => {
      this.results = data;
    });
  }
}
```

