# [Learning Angular not for fun and zero profit](https://angular.io)

As my side project (based around our hamster) needs a front end I am finally required to learn a bit of front end programming

I have chosen to pick up Angular. Only because I know other people who use it professionally, it is popular so StackOverflow can help me and I can use TypeScript so I get Types which I can not live without

I am using Pluralsight to learn Angular and looking to use Angular 8.

These are notes on the material covered to aide in remembering, I learn better when I write it out for potentially others to read as one must understand what they are writing

## Running an Angular App

Typescript is Transpiled into Javascript

## Single Page Application (SPA)

Angular is a SPA. It has a single index.html that references a single directive name (a directive name is a custom html tag). This this loads the rest of the app

## [Modules](https://angular.io/guide/architecture-modules)

Angular is modular and consists of many library modules such as:

* `@angular/core`
* `@angular/animate`
* `@angular/common`
* `@angular/router`

Angular apps consist of a Root Angular module that has multiple components defined in the module. You can have multiple modules in an application but there is always a root module. Typically this route module is called AppModule.

The file structure for a module is \<*module name*>.module.ts

So for the root module this would typically be `app.module.ts`

In a module we describe the component dependencies of the module

Example module

```typescript

@NgModule({
  declarations: [
    AppComponent,
    StarComponent
  ],
  imports: [
    BrowserModule,
    FormsModule
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

* `declarations`: used to declare our components in our module
* `imports`: external modules we want available to all our components in this module
* `bootstrap`: The component to start (launch)

## [Components](https://angular.io/guide/architecture-components)

Angular modules consist Components. Components can be nested within each other.

A Component consists of:

* A Template
* * This is a View layout
* * Created using HTML
* * Uses bindings an directives
* Class
* * Code to support the view
* * Written in TypeScript
* * Has properties (data)
* * Has methods (logic)
* Metadata
* * Extra data for Angular
* * Defined with **Decorator**

Example Component with inline template (Normally use templateUrl in the decorator to point to an html file)

```typescript
import { Component } from '@angular/core';

//This is the Metadata, this uses the @Component decorator.
@Component({
    // The selector is the name of the html tag that should be used to reference this component from a template. A custom HTML Tag is known as a directive name
    selector: 'pm-star',
    //Here we inline the template though usually would use:
    //templateUrl: './star.component.html'
    template: `<div style="text-align:center">
  <h1>
    Hello andWelcome to {{pageTitle}}!!
  </h1>
  ... Starter Files ...
</div>`
})
// This is the class. The file is called star.component.ts
export class StarComponent {
// export makes the class available to other components

// Defining some properties
    pageTitle: string = 'Hello World'
}
```

The Root Component is usually called the app Component

Typically in Angular the file structure is: \<*feature*>.component.\<file type>

Example being the Star component that we use above the files we have are:

* star.component.ts - The Class
* star.component.html - The HTML template
* star.component.css - The style sheet uses

### Decorator

A decorator is a **function** that adds meta data to a task. If you look at the above example with the `@Component({...})`.

Here you see that the @Component takes an Object as a parameter to the function call. The Object is defined brackets using the `{}`

## Binding

Coordinates communication between the components class and template and passes information between the two

* Interpolation -> One way binding, class to template
* Property binding -> One way binding, class to template
* Event binding -> One way binding, template to class
* Two way binding -> binding between template and class and class to template

### Interpolation

This is a one way binding from the class to the template

Interpolation takes place in the template using `{{}}`. It is called a template expression.

e.g. 

```html
<h1>{{pageTitle}}</h1>
```

This will call a property or a method. We can do any expression

e.g.

```typescript
{{'Title ' + pageTitle}}
{{20 * 4 + 1}}
{{'Title ' + getPageTitle()}}
<h1 innerText={{pageTitle}}></h1>
```

**Note** interpolation converts to property binding but it is a onetime transform and the evaluated to a string

### Property Binding

This is a one way binding and will evaluate the expression. The evaluation will be to the result and not be to a string. This is useful in the following case when a non-string result is required

```typescript
export class AppComponent {
    isDisabled: boolean = true;
}

<button [disabled]='isDisabled'>OK</button>             //Data binding
```

Property binding uses `[]` brackets

### Event Binding

This is when an event is sent from the template to the class. The component is "listening" for user actions

The syntax uses the following (*Target Event*)='*Template Statement*'. The template statement is usually a method call. A real example is:

`<button (click)='toggleImage()'>`

Here we notice the use of `()` brackets to signify event binding

### Two way event binding

This is typical when we are taking a user input and want to update values based on input.

The ngModel directive is used in the following example. The ngModule is in the `FormsModule` which we need to add as an import to the Root Module.

```html
<input [(ngModel)]='listFilter'/>
<div >Filter by: {{listFilter}}</div>
```

```typescript
export class ListComponent {
  listFilter : string = 'searchString'
}
```

In the example we see the `[()]` which is the property binding `[]` and the event binding `()` which makes sense with the two way binding.

This would tke the user input and then update t

## [Pipes](https://angular.io/guide/pipes)

These are used in interpolation to transform the value of a bound property before it is displayed. They work like Unix pipes and there are pre-built pipes

e.g.

```html
<td>{{product.productName | uppercase}}</td>
<td>{{product.price | currency:'GBP':'symbol':'1.2-2'}}</td>
```

## [Angular structural directives](https://angular.io/guide/structural-directives)

Structural directives modifies the structure of the layout or view

### *ngIf directive and *ngFor directive

Example
```html
<div class="table-responsive"
     *ngIf='filteredProducts && filteredProducts.length'>
    <table class="table">
        <thead>
            <tr>
                <th>Product</th>
            </tr>
        </thead>
        <tbody>
            <tr *ngFor='let product of filteredProducts'>
                <td>{{product.productName | uppercase}}</td>
            </tr>
        </tbody>
    </table>
</div>
```

We can see the *ngIf where a statement is evaluated and if it is true the DOM will be displayed else it will not

The *ngFor will evaluate the expression and then we can use interpolation in the sub DOM and access it's properties

### [Angular Component Lifecycle](https://angular.io/guide/lifecycle-hooks)

Angular Components have a life cycle (like Game Objects in Unity3D). It is possible to hook into the lifecycle of a component

The full lifecycle sequence can be seen here https://angular.io/guide/lifecycle-hooks.

To hook into the lifecycle we implement an interface on the class

The ones the we need to concentrate on are:

* ngOnChanges()
* ngOnInit()
* ngOnDestroy()

### On changes (ngOnChanges)

Used for changes **after** Angular has set any data bound input

```typescript
import { Component, OnChanges } from '@angular/core';
export class ProductListComponent implements OnChanges {
  ngOnChanges(): void {
      console.log('Called Changes')
  }
}
```

### On init (ngOnInit)

Used to initialize the component and this is where initialization should happen when calling things such as a REST service for initialization. This is the place instead of the class constructor

```typescript
import { Component, OnInit } from '@angular/core';
export class ProductListComponent implements OnInit {
  ngOnInit(): void {
      console.log('Called Init')
      this._products = this.productService.getProducts();
  }
}
```

### On destroy (ngOnDestroy)

This is used for cleanup of a component. Maybe when a component is closed we want to send a message to a backend service

```typescript
import { Component, OnDestroy } from '@angular/core';
export class ProductListComponent implements OnDestroy {
  ngOnDestroy(): void {
      console.log('Called Destroy')
  }
}
```

## Passing data between components

Usually it is needed to communicate between a Parent and Child component. An event emitter can be passed information and then to emit events in the child (aka nested) component it hooks into an event handler defined in the parent.

### Passing data to nested component `@Input`

To decalare that a value can be passed to a component we use the `@Input` decorator

```typescript
import { Component, Input } from '@angular/core';
@Component({
    selector: 'pm-star'
})
export class StarComponent {

    @Input()
    rating: number;
}
```

Passing to the nested component uses **Property Binding** to pass in the value. We can see here how this is done

```html
<pm-star [rating]='product.starRating' ></pm-star>
```

This is the only time we can target a nested components property when it has the @Input decorator

### [Event Emitter](https://angular.io/api/core/EventEmitter) @Output

If the nested component wants to raise an event with its parent then we need to use the EventEmitter. The component exposes an event for the parent. The EventEmitter uses Generics to pass back the event type.

The output must be of an event type

```typescript
import { Component, Input, Output, EventEmitter } from '@angular/core';
@Component({
    selector: 'pm-star'
})
export class StarComponent {

    @Input()
    rating: number;

    @Output()
    starNotify: EventEmitter<string>() = new EventEmitter<string>();

    starOnClick(): void {
        this.starNotify.emit('I was clicked!!!')
    }
}
```

The emit() method is called to emit the event

This is using the **Event Binding** in the HTML to receive the event. We can see in the following example that the parameter type of the `event` is a string. This type is the same as that of the EventEmitter type.

Notice in the event binding that the parameter is defined with `$event`. The `$` is important to remember to bind to the result

```html
<pm-star [rating]='product.starRating' (starNotify)='onNotify($event)' ></pm-star>
```

```typescript
export class ParentComponent {
  onNotify(event: string): void {
        console.log(`Got event! Message was ${event}`)
        this.pageTitle = event
    }
}
```

## [Dependency injection](https://angular.io/guide/dependency-injection)

Dependency injection is done by declaring a service using the `@Injectable`. The Class that wants to use this declares the type in its constructor and it will be pulled in.

A service is registered into the Angular Injector and can be set a different injector levels. It can be injected at the root level `provider` (which it is by default) or it can be injected with a specific injector provider name to limit its scope.

The file format for a service is \<service name>.service.ts e.g. `product.service.ts`

An example of a Service is:

```typescript
import { Injectable } from '@angular/core';
// @Injectable() these are both equivalent
@Injectable({providedIn: 'root'})
export class ProductService {

}
```

As the service is registered we can now use it and pick it up in a class

```typescript
@Component({
    selector: 'pm-products',
    templateUrl: './product-list.component.html',
})
export class ProductListComponent {

  // These two are equivalent. Adding private in constructor adds it as a property to the class and is short hand
  // private _productService: ProductService;
  // constructor(productService: ProductService) {
  //   this._productService = productService.getProducts();
  // }
  constructor(private productService: ProductService) {}
}
```
