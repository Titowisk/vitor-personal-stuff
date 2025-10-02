# Angular
- npm install: install dependencies from package.json
- angular is based on components
- the idea behind angular is to build a components tree
    - AppComponent beeing the root component

# Components

## app.component.ts

- selector: defines the placeholder to be replaced by the component
    - the placeholder looks like this: `<app-root></app-root>`
- standalone ?
- imports ?
- templateUrl: path to the html file of the component
- styleUrl: path to the css file of the component

- a class can be defined an used to create variables that are maid available for the components 

## app.component.html

### Pipes 
Can be used to transform data
Example: `<time datetime="">{{ task?.dueDate | date }}</time>`

## app.component.css

## Property Binding
- Use [] to bind html properties to angular handling logic
- Property binding really targets the underlying DOM object property (in this case a property that's also called src) and binds that.
- This might look like a subtle detail (and often it indeed doesn't matter) but it's important to understand this difference between element attributes and property. This article can help with understanding this difference.
- article: https://jakearchibald.com/2024/attributes-vs-properties/

before: `<img src="/path/to/image">`

after: `<img [src]="/path/to/image">`

## Input decorator
Manages input properties for the component.
Custom properties can be defined by a custom component

## Output
Manages output properties from the component.

## Wrapping components
You can use components to create standard ui or behavior that can be shared with other components using
**Content Projection**:
`ng-content`

## Component Selectors
- https://angular.dev/guide/components/selectors 

When dealing with component re-usage, sometimes its better to use a different kind of selector, to avoid unnecessary elements in the DOM.
The angular selector can cover dom elements, class, etc. (works like a css selector)

# Directives
You can enhance elements by adding Directives to them.
Directives, unlike components, don't have a template!

`<input ngModel>` An element enhancement that helps with extracting (or changing) user input values.



> Components are directives with templates.

# Events

To create an event you should create a function on the class and create an event on the html element

```javascript
// function
onSelectUser() {
    console.log('Selected user:', this.selectedUser);
  }

```

```html
<button (click)="onSelectUser()"></button>
```

# State

## zone.js (first option)
suported since Angular 2
zone.js manages states changes by listening to all events. When an event occurs, it will check for any changes to apply. It's automatic.

It creates zones around angular components and check each one to manage state changes.

## signals (second option)
supported since Angular 16
Requires usage of special "signal" instructions and code
Signals are trackable data containers. Angular manages subscriptions to the signal to get notified about value changes.

Its more efficient because the component will notify angular and avoid the zone logic above.

signal + computed methods must be used to properly work with the feature.

you can define custom component properties by using `input` from signal. Must no be confused with `Input`.

# Forms

## Two way binding
`<input [(ngModel)]="someProperty">` applies two way binding `[()]` to the `someProperty` in the .ts file.

# Services
A way to encapsulate logic from components and keep them small

# Modules
Angular modules exist for historic reasons. 
`standalone` components are new, before them modules were used.
standalone components are the recommended way of building components
```js
@Component({
  selector: 'app-task',
  standalone: true,
  imports: [CardComponent, DatePipe],
  templateUrl: './task.component.html',
  styleUrl: './task.component.css'
})
```
From Angular 19 going forwardm standalone components are default.
Before that, Modules were the default

# Debugging

## Chrome Dev Tools
You can use chrome dev tools to debug the application because it can know about your code. Going to the `Source` tab of the devtools it's possible to find it and put breakpoints and debug

## Angular Dev Tools
A more robust and focused tools to debug angular applications

# Splitting Components
Splitting components is a matter of **separation of concerns**.

Every component should only do "one thing".

Separation of Concerns vs Simplicity & Code Colocation

# Good Practices

- keep your components as lean as possible: this can be achieved using services
- whenever you find code, logic or markup duplication there's a opportunity to create a reusable/shared component to deal with that
  - it depends on the context whether you should do it or not