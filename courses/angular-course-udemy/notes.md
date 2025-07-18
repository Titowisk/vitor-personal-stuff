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

# Good Practices

- keep your components as lean as possible: this can be achieved using services