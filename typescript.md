# EXOS TYPESCRIPT CODING STANDARDS

## Naming Conventions
* Use PascalCase for Class, Interface, AbstractClass, Enum, Module names and data types
* Use camelCase for variable and function names

## Type Hinting

When writing code in TypeScript, you should explicitly add types to your variables and functions when the compiler cannot infer the type, since when a variable is not annotated and type cannot be inferrred, it is given an implicit type `any`.

````typescript
function calcTax(state: string, income: number, dependents: number) : number {
 
    if (state === 'NY'){
        return income * 0.06 - dependents * 500;
    } else if (state ==='NJ'){
        return income * 0.05 - dependents * 300;
    }
}
````
## Type Usage
* Cast types using `as` syntax for clarity.  Diamonds (`< >`) should be used for generics only.
```typescript
let myObject: TypeA;
let otherObject: TypeB;

myObject = otherObject as TypeA;    // using as keyword is better
myObject = <TypeA> otherObject;     // using <> should be saved for generics
```
* Do not use `any`.  You can use a union type instead.  Using a union type will allow your IDE's autocomplete feature prompt you with allowed argument types.
````typescript
let padding: string | number;
````
* Use `type` over `interface` for new types.  Use `interface` for inheritance purposes only.  
````typescript
type Patient = {
  name: string;
  height: Foot;
  weight: Pound;
}

let patient: Patient = {
    name: 'Joe Smith',
    height: 5,
    weight: 100
}

````
* Always explicitly define a return type.
```typescript
// bad
function getPerson(name: string) {
    return new Person(name);
}

// good
function getPerson(name: string): Person {
    return new Person(name);
}
```
## Note Regarding Using enum
If you use the keyword `const` while declaring an `enum`, its values will in-lined and no JavaScript will be generated.  Take a look at the following example code.
```typescript
 enum Direction {
    Up = "UP",
    Down = "DOWN",
    Left = "LEFT",
    Right = "RIGHT"
}
const theNextMove = Direction.Down;
```

Here is the JavaScript that is generated when declaring an enum without `const`
````typescript
"use strict";
var Direction;
(function (Direction) {
    Direction["Up"] = "UP";
    Direction["Down"] = "DOWN";
    Direction["Left"] = "LEFT";
    Direction["Right"] = "RIGHT";
})(Direction || (Direction = {}));
const theNextMove = Direction.Down;
````
Now, here is the JavaScript that is generated if you add the `const` keyword before `enum`.
````typescript
"use strict";
const theNextMove = "DOWN" /* Down */;
````
Using `const` with `enum` results in more concise JavaScript, but keep in mind that because there is no JavaScript code to represent your `enum`, you may run into some limitations. For example, you won’t be able to retrieve a numeric enum member’s name with its value.
## Vue.js Single File Components

Currently, the standard is to use the Options API when writing Vue.js single-file components using TypeScript.  When using the Options API, the difference between writing the component in es6/next and TypeScript is that you must export your component differently in order to support type inference.

```typescript
<template>
</template>

<script>
import Vue, { VNode } from 'vue'

export default Vue.extend({
  data () {
    return {
      msg: 'Hello'
    }
  },
  methods: {
    // need annotation due to `this` in return type
    greet (): string {
      return this.msg + ' world'
    }
  },
  computed: {
    // need annotation
    greeting(): string {
      return this.greet() + '!'
    }
  },
  // `createElement` is inferred, but `render` needs return type
  render (createElement): VNode {
    return createElement('div', this.greeting)
  }
})
</script>

<style>
</style>
```
This style of writing single-file components will remain compatible with Vue.js 3.  However, when we switch to version 3, we may reevaluate the [Composition API](https://vue-composition-api-rfc.netlify.com/).  
