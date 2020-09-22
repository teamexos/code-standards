# EXOS Front-End Code Standards and Practices

## General Rules
Please use the included [`.editorconfig`](.editorconfig) file to enforce the following rules for all files.

 * Use spaces for tabs
 * Tab size is 4 spaces, except for `.json` files which is 2 spaces.
 * No trailing whitespace, except for Markdown files (`.md`).
 * Add a new line to the end of files.

## Prettier
On projects that utilize Prettier (https://github.com/prettier/prettier) for code formatting, the JavaScript code will be automatically formatted to match standards using a pre-commit hook. The following is a listing of the Prettier option settings that are currently used:
 * `print-width 100`
 * `tab-width 4`
 * `semi-colons true`
 * `single-quote true`
 * `trailing-comma es5`
 * `bracket-spacing true`

On projects that do not currently support the Prettier pre-commit hook, you can configure your IDE to utilize Prettier by configuring a plug-in.  Instructions for most IDE's can be found at [https://github.com/prettier/prettier#editor-integration](https://github.com/prettier/prettier#editor-integration).

## HTML

### Attributes
`ng-*` attributes before all other attributes. Followed by native attributes with quotes. Attributes with no `=""` go last.
Always use double quotes for HTML attributes.

```html
<div ng-if="" class="" data-aid-field></div>
```

For tags with more than one attribute, stacking is the preferred method. The first attribute should be on a new line and indented one level from the opening tag. Each attribute should be on it's own line. The closing tag should be at the same indent level as the opening tag.

```html
<div
    ng-if=""
    class=""
    data-aid-field>
</div>
```

Content within the element with stacked attributes should start at the same indent level as the attributes.

```html
<div
    ng-if=""
    class=""
    data-aid-field>
    Something here
</div>
```

### Template Variables
Always include spaces between curly braces.
```html
{{ Controller.variable }}
```

## JavaScript

### General Rules
 * Always use single quotes
 * Use camelCase for all variable names
 * When creating data objects, use snake case for key names
 ```javascript
 const obj = {
   user_id: 1,
   first_name: 'John',
   last_name: 'Doe',   
 }
 ```
 * The ? and : in a ternary conditional must have space on both sides.
 * `if` / `else` / `for` / `while` / `try` blocks should always use braces and always go on multiple lines.
 * No space before opening parenthesis in function declaration.

```javascript
if (isEnabled) {
    ...
}

const myFunc = function(arg) {
    ...
};
```
* Use pure functions whenever possible (functions take arguments and return a value without mutating values outside of the function's scope).  An exception would be when creating handler functions that are triggered by DOM events.    

```javascript
// bad
let itemList  = [0, 1, 2, 3, 4]

function returnFirst(arr) {
  return arr.shift()
}

const firstItem = returnFirst(itemList)
console.log(itemList)  \\ [1, 2, 3, 4]
```

```javascript
// good
let itemList = []

function returnFirst(arr) {
  return arr.slice(0,1)
}

const firstItem = returnFirst(itemList)
```

### Recommendations (for Webpack/Babel projects)
 * When working in a project that utilizes the Babel transpiler, use string templating instead of concatenation whenever possible.

 ```javascript
 const path = `/api/${moduleName}/`
 ```
 * Use fat arrow functions whenever possible (`const nameList = itemList.map(item => item.name)`)
 * Don't use `for` loops unless absolutely necessary and instead use more a more declarative coding style (`map()`, `filter()`, `reduce()`)
 * Use block level scoping (`const` or `let`) for declaring variables instead of `var` whenever possible.  For variables that will have values assigned just once, use `const` instead of `let`.
 * When handling promises, use `async`/`await` whenever feasible, instead of callbacks.  However, always wrap the `await` in a `try`/`catch` block when handling API responses.
 ````javascript
const getLocations = async () => {
  try {
    const resp = await RequestPromise(options)
    return resp;
  }
  catch (err) {
    console.log(err)
  }
}
```` 
### JavaScript Style Guide
Please refer to the [EXOS JavaScript Style Guide](https://github.com/teamexos/code-standards/blob/master/javascript.md) for detailed coding standards.  

### Other JS Recommendations
 * Avoid using Array.prototype.forEach() to iterate through and mutate data arrays. Instead, use functions such as .map() and .reduce() that return new arrays.
 * Also avoid using functions that mutate data structures such as Array.prototype.reverse().
 * Within AngularJS Services and Controllers, functions should not mutate the existing values and should instead take inputs and return a new value whenever possible.  Side effects should be avoided.
 * When working with Vue.js, add new properties to data objects using  Vue.set().
 * Use strict mode for Vue.js/Vuex (only mutate data using Vuex mutations) and MobX (only mutate data using actions).


## TypeScript
We currently use TypeScript in all of our Vue.js projects.  Please refer to the [EXOS TypeScript Coding Standards](https://github.com/teamexos/code-standards/blob/master/typescript.md).  For additional information regarding TypeScript, please refer to the official TypeScript [documention](https://www.typescriptlang.org/docs/handbook/basic-types.html).

## Vue.js

### Vue.js Style Guide
We follow style guide standards, all the way up to Priority D in the official [Vue.js Style Guide](https://vuejs.org/v2/style-guide/), with the following exceptions:  
* [Base component names](https://vuejs.org/v2/style-guide/#Base-component-names-strongly-recommended)
* [Single instance component names](https://vuejs.org/v2/style-guide/#Single-instance-component-names-strongly-recommended)

For state management in greenfield Vue.js projects, we utilize [Vuex](https://vuex.vuejs.org/en/). Please refer to the Vuex style guide in the FE Engineering Google Site. For projects that mix Vue.js and AngularJS, we utilize the [MobX](https://mobx.js.org/) library to add reactivity.


### XSS in Vue.js
Cross-site scripting (XSS) enables attackers to inject client-side scripts into web pages viewed by other users.  In order to avoid creating XSS vulnerabilities in the front end code, at the very
minimum the following steps should be taken:

*  In situations where  `v-html` was required in order to output HTML in a template, a sanitization library must be used and
the code must be subjected to strict scrutiny during the code review process.
* Injection of raw user input directly into an Vue.js template should never be permitted without sanitization.  

### Vue.js 3 Migration Standards
We will be migrating to Vue.js 3 as soon as Nuxt.js version 3 is out.  In order to facilitate the eventual Vue.js 3 migration, we should avoid the following breaking changes from 2.x:
* [Global Vue API is changed to use an application instance](https://v3.vuejs.org/guide/migration/global-api.html#a-new-global-api-createapp)
* [Global and internal APIs have been restructured to be tree-shakable](https://v3.vuejs.org/guide/migration/global-api-treeshaking.html#_2-x-syntax)
* [`v-model` usage on components has been reworked](https://v3.vuejs.org/guide/migration/v-model.html#overview)
* `<template v-for>` key should be placed on the `<template>` tag (rather than on its children).
  [https://v3.vuejs.org/guide/migration/key-attribute.html#overview](https://v3.vuejs.org/guide/migration/key-attribute.html#overview)
* If used on the same element, v-if will have higher precedence than v-for [https://v3.vuejs.org/guide/migration/v-if-v-for.html#overview](https://v3.vuejs.org/guide/migration/v-if-v-for.html#overview)
* [`v-bind="object"` is now order-sensitive](https://v3.vuejs.org/guide/migration/v-bind.html#overview)
* [`ref` inside `v-for` no longer register an array of refs](https://v3.vuejs.org/guide/migration/array-refs.html)
* [Functional components can only be created using a plain function](https://v3.vuejs.org/guide/migration/functional-components.html#overview)
* [functional attribute on single-file component (SFC) <template> and functional component option are deprecated](https://v3.vuejs.org/guide/migration/functional-components.html#overview)
* [Async components now require defineAsyncComponent method to be created](https://v3.vuejs.org/guide/migration/async-components.html#overview)
* [Render function API changed](https://v3.vuejs.org/guide/migration/render-function-api.html#overview)
* [`$scopedSlots` property is removed and all slots are exposed via $slots as functions](https://v3.vuejs.org/guide/migration/slots-unification.html#overview)
* [Special `is` prop usage is restricted to the reserved `<component>` tag only](https://v3.vuejs.org/guide/migration/custom-elements-interop.html#customized-built-in-elements)
* Please refer to the Vue.js [documentation](https://v3.vuejs.org/guide/migration/introduction.html#breaking-changes) for additional breaking changes

### Internationalization in Vue.js
If a project's requirement call for internationalization or support multiple languages and/or locales, the plugin Vue-i18n should be used along with a front-end JavaScript `translations.js` file which holds the needed translations is the following format:

```export default {
    en: {
        message: {
            login: 'Login',
        },
    },
    ja: {
        message: {
            login: '世界',
        },
    },
};```

In order to handle error messages from the server, we should use error codes rather than error messages from the API in order to translate messages on the front end.

## AngularJS

### AngularJS 1.5+ (with ES6+)
For AngularJS projects utilizing a version of AngularJS higher than 1.5 and a transpiler for using JavaScript ES6 or beyond, refer to [Todd Motto's AngularJS Styleguide](https://github.com/toddmotto/angularjs-styleguide).

For more detailed guidelines for developing in AngularJS, please refer to the [AngularJS Coding Standards](https://github.com/teamexos/code-standards/blob/master/angularjs.md)
## CSS ([SCSS](http://sass-lang.com/))

### General Rules

 * Use shorthand when possible.
 * Use kebab case when naming classes (example - `name-of-class`)
 * Wrap .scss files in a selector for that file.

```scss
.foo {
    bottom: 0;
    color: #000;
    display: block;
    font-size: 12px;
    margin: 0 auto;
    position: absolute;
    top: 0;
    width: 100px;
}
```

### Extend

All `@extend` definitions should be at the top of the property list with a new line separating it from the native properties. If multiple `@extend` rules are used they should be in alphabetical order.

```scss
.foo {
    @extend .clearfix;

    bottom: 0;
    color: #000;
    display: block;
    font-size: 12px;
    margin: 0 auto;
    position: absolute;
    top: 0;
    width: 100px;
}
```

### Mixins

Mixins should be included at the end of the property definition with a new line separating the mixins from the native properties. If multiple mixins are used they should be in alphabetical order.

```scss
.foo {
    bottom: 0;
    color: #000;
    display: block;
    font-size: 12px;
    margin: 0 auto;
    position: absolute;
    top: 0;
    width: 100px;

    @include bg-gradient-default();
    @include translate(0, -50%);
}
```

## Git Commit Messages

Commit messages should follow the following format.

```
Example:
JT-1234: Added table of contents
* Ticket Number
* short description of work done starting with past tense action verb


```

## Development Tips and Tricks
You can find useful tips and tricks related to development and debugging in our [Tips and Tricks](https://github.com/teamexos/code-standards/blob/master/dev-tips.md) document.

## The Process for Determining Whether or not to Add a New Library to a Project

* Start a thread in the #fe-developers Slack channel so that we can discuss the inclusion of this library as a team.
    * Please state the reason why the library is needed (timeline, complexity or benefits from adding a library that is an industry standard and will be useful elsewhere in Journey UI)
    * Include information about how much the vendor bundle size would be increased by the addition of the new library.  You can use the [Webpack Bundle Analyzer](https://github.com/webpack-contrib/webpack-bundle-analyzer) for this.
    * During this discussion, team members should be able to add their past experiences using the library and/or make alternate recommendation.
    * When adding the library to the project's `package.json` file, make sure to use an exact version number or use `~` with the version number instead of `^`.
* Once the library has been informally approved in the previous step, add all of the relevant information (bundle size addition, etc.) to the relevant JIRA ticket.
* Be sure to try a Webpack build to confirm that the new module will build properly using `npm run build`.


