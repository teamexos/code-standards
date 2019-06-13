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
 * Use one `const` or `let` declaration per variable or assignment. [According to the Airbnb JavaScript style guide](https://github.com/airbnb/javascript#variables--one-const):
  >>  Why? It’s easier to add new variable declarations this way, and you never have to worry about swapping out a ; for a , or introducing punctuation-only diffs. You can also step through each declaration with the debugger, instead of jumping through all of them at once.
 ```javascript
 // bad
const items = getItems(),
    goSportsTeam = true,
    dragonball = 'z';

// bad
// (compare to above, and try to spot the mistake)
const items = getItems(),
    goSportsTeam = true;
    dragonball = 'z';

// good
const items = getItems();
const goSportsTeam = true;
const dragonball = 'z';
```
 * When using ES6 imports, only import what you will use.

 ```javascript
 import { autorun } from 'mobx'
 ```

### Guidelines
 * Avoid using Array.prototype.forEach() to iterate through and mutate data arrays. Instead, use functions such as .map() and .reduce() that return new arrays.
 * Also avoid using functions that mutate data structures such as Array.prototype.reverse().
 * Within AngularJS Services and Controllers, functions should not mutate the existing values and should instead take inputs and return a new value whenever possible.  Side effects should be avoided.
 * When working with Vue.js, add new properties to data objects using  Vue.set().
 * Use strict mode for Vue.js/Vuex (only mutate data using Vuex mutations) and MobX (only mutate data using actions).


## TypeScript

## Usage
Currently, we are using just basic types.  For more information, please refer to the official TypeScript [documention](https://www.typescriptlang.org/docs/handbook/basic-types.html).

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

### IIFE
Wrap components in an immediately-invoked function expression (IIFE), passing in necessary global objects as parameters. Include the `'use strict'` expression to force strict mode. NOTE:  This is not necessary for projects using Babel/Webpack.

```javascript
(function(angular) {
    'use strict';

    var module = angular.module('WebApp');

}(angular));
```

## AngularJS

### AngularJS - Component Structure
 * Components should be as modular as possible.
 *  If a component has more than one file it should be in it's own directory.
 * The name of the directory and it's files should match the name of the component in kebab-case.

```
|-- app
    |-- path-details
        |-- path-details.js
        |-- path-details.html
        |-- path-details-controller.js
        |-- path-details-service.js
        |-- path-details.scss
```

The main JS file should be used to register the component, `module.config`, `module.run`, and if the component is a directive, `module.directive`.
```javascript
(function(angular) {
    'use strict';

    var module = angular.module('MyApp.PathDetails', []);

    module.config(Config);

    Config.$inject = [];

    function Config() {

    }

    module.run(Run);

    Run.$inject = [];

    function Run() {

    }

    module.directive('someDirective', Directive);

    Directive.$inject = [];

    function Directive() {
    }

})(angular);
```

Each component should be it's own Angular module. The naming convention should be UpperCamelCase with any parent modules prefixed separated with a period.

If the module has no dependencies an empty array on the same line should be used.
```javascript
var module = angular.module('ParentModule.ChildModule.GrandChildModule', []);
```

If the module has one or more dependencies they should be stacked on new lines like so.
```javascript

var module = angular.module('ParentModule.ChildModule.GrandChildModule', [
    'ui.router',
    'ui.bootstrap'
]);

```

### AngularJS - Dependency Injection

All Angular modules should define their dependencies using the [$inject property annotation](https://docs.angularjs.org/guide/di#-inject-property-annotation).

```javascript

module.controller('MyController', Controller);

Controller.$inject = ['MyService'];

function Controller(MyService) {

}

```

Modules with multiple dependencies should define each on a new line.
```javascript

module.controller('MyController', Controller);

Controller.$inject = [
    'MyService',
    'YourService'
];

function Controller(
    MyService,
    YourService
) {
    ...
}

```

All modules prefixed with a dollar sign should come before all other modules.
```javascript

module.controller('MyController', Controller);

Controller.$inject = [
    '$http',
    'MyService',
    'YourService'
];

function Controller(
    $http,
    MyService,
    YourService
) {
    ...
}

```

The following is an example of how to do DI in a Babel/Webpack project such as the current version of Journey UI (use `ngInject`
instead of `$inject()`).

```javascript
class OverviewController {

    constructor(
        $state,
        $uibModal,
        WorkoutDetailService,
        WorkoutProgressService
    ) {
        'ngInject';

        this.$state = $state;
        this.$uibModal = $uibModal;
        this.WorkoutDetailService = WorkoutDetailService;
        this.WorkoutProgressService = WorkoutProgressService;
    }
    ...
}
```


### AngularJS - Routes ([ui-router](http://angular-ui.github.io/ui-router/site/#/api/ui.router))

* URLs should end with a slash.
* Always include templates in separate files and use `templateUrl`. Exception to this is when defining an abstract state and the template is `<ui-view/>`'.
* Use `controllerAs` property. `controllerAs` name should be a shorter version of the controller name without the "Controller" suffix.
* Multiple states should be defined on their own and not chained.
* Resolve methods should follow the same dependency injection rules as above.

```javascript

module.config(Config);

Config.$inject = ['$stateProvider'];

function Config($stateProvider) {

   $stateProvider.state('home', {
       abstract: true,
       template: '<ui-view/>',
       ...
   });

    $stateProvider.state('home.list', {
        template: 'home/home-list.html',
        controller: 'HomeController',
        controllerAs: 'Home',
        resolve: {
            fetchData: fetchData
        }
    });

    fetchData.$inject = ['MyService'];

    function fetchData(MyService) {

        return MyService.fetchData();

    }

}

```

The following is an example of how this can be accomplished in a Babel/Webpack project:


```javascript
import angular from 'angular';
import component from './detail.component.js';
import componentPrint from './detail-print.component.js';

const detail = angular
    .module('Journey.Workouts.Detail', [])
    .component('workoutDetail', component)
    .component('workoutDetailPrint', componentPrint)
    .config(($stateProvider) => {
        'ngInject';

        $stateProvider.state('private.workouts.detail', {
            abstract: true,
            url:':workoutId/:phase/:week/:day',
            template: '<ui-view autoscroll/>',
            params: {
                workoutId: null,
                phase: { squash: true, value: null },
                week: { squash: true, value: null },
                day: { squash: true, value: null }
            },
            resolve: {
                details: ($transition$, WorkoutDetailService) => (
                    WorkoutDetailService.fetch($transition$.params().workoutId)
                )
            }
        });

        $stateProvider.state('private.workouts.detail.home', {
            url: '/',
            component: 'workoutDetail',
            indexTemplate: {
                bodyClass: 'workouts',
                fullWidth: true,
                pageTitle: 'Workout Library'
            }
        });

        ...
}
```


### AngularJS - Services

* Names should be UpperCamelCase and contain the `Service` suffix. `MyAppService`
* Each service returns a public object, named after the service.

```javascript

module.factory('MyService', Service);

function Service() {

    var MyService = {};

    MyService.getDetails = function() {
        ...
    };

    return MyService;

}
```

* Create a service using an ES6 class when working in a Babel/Webpack AngularJS project.

```javascript

class WorkoutDetailService {

    constructor(
        $location,
        $state,
        $window,
        $uibModal
    ) {
        'ngInject';

        this.$location = $location;
        this.$state = $state;
        this.$window = $window;
        this.$uibModal = $uibModal;
    }

    fetch(workoutId) {
        if (['complementary', 'corrective'].indexOf(workoutId) > -1) {
            return this.getMovement(workoutId);
        } else {
            return this.getWorkoutDetails(workoutId).then(::this.mergeProgress);
        }
    }

    ...

}

export default WorkoutDetailService;
```

### AngularJS - Controllers

* Names should be UpperCamelCase and contain the `Controller` suffix.  `MyAppController`
* Controllers should be as slim as possible. Keep logic in Services and Directives.
* Private controller functions should go towards the bottom.
* Since we're using `controllerAs` in our routes we should avoid using `$scope` and bind to `this` via a `vm` variable.

```javascript

module.controller('MyController', Controller);

Controller.$inject = ['MyService'];

function Controller(MyService) {

    var vm = this;

    vm.firstName = MyService.details.firstName;

}

```

* In Webpack/Babel AngularJS projects, use an ES6 class to create a controller.  Make sure to leave logic out of the constructor.

```javascript
class OverviewController {

    constructor(
        $state,
        $uibModal,
        WorkoutDetailService,
        WorkoutProgressService
    ) {
        'ngInject';

        this.$state = $state;
        this.$uibModal = $uibModal;
        this.WorkoutDetailService = WorkoutDetailService;
        this.WorkoutProgressService = WorkoutProgressService;
        this.selectedPhase = parseInt($state.params.phase);
        this.selectedWeek = parseInt($state.params.week);
        this.selectedDay = parseInt($state.params.day);
        this.data = WorkoutDetailService.data;
    }

    $onInit() {

        const equipment = this.WorkoutDetailService.getEquipment();
        this.equipment = Array.isArray(mobx.toJS(equipment)) ? equipment : [];
        this.isOverviewInit = true;

        // Set up MobX reaction to handle changes to Workout Phase Progress Array
        this.dispose = autorun(() => {
            this.phases = this.initializePhasesArray(this.WorkoutDetailService.workoutProgress);
            this.workoutIsComplete = this.WorkoutDetailService.workoutIsComplete;
            this.busyCompleting = this.WorkoutProgressService.busy.setWorkoutComplete;
            this.progressStats = this.WorkoutProgressService.getProgressStats(this.data.workout);
            this.progressPct = this.progressStats.percentComplete;
        });

    }

    $onDestroy() {
        this.dispose();
    }

    ...

}
```

### AngularJS - Directives

* Use `bindToController` property to avoid using `$scope` in controller.
* Follow rules of component structure, dependency injection, and controllers.

```javascript

var module = angular.module('MyApp.MyFeature', []);

module.directive('myFeature', Directive);

function Directive() {
    return {
        restrict: 'E',
        replace: true,
        controller: 'MyFeatureController',
        controllerAs: 'MyFeature',
        bindToController: true,
        templateUrl: 'app/home/my-feature/my-feature.html',
        link: function(scope, element, attrs, controller) {
            ...
        }
    };
}


```

### XSS in AngularJS
Cross-site scripting (XSS) enables attackers to inject client-side scripts into web pages viewed by other users.  In order to avoid creating XSS vulnerabilities in the front end code, at the very
minimum the following stesp should be taken:

*  In situations where  `ng-bind-html` or `$sce.trustAsHtml()` was required in order to output HTML in a template, strict scrutiny
must be applied during the code review of such code.
* Injection of raw user input directly into an AngularJS template should never be permitted without sanitization.  

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

## Commit Messages

Commit messages should follow the following format.

```

JT-1234: Ticket name
 * short description of work done
 * short description of work done

```

## The Process for Determining Whether or not to Add a New Library to a Project

* Start a thread in the #fe-developers Slack channel so that we can discuss the inclusion of this library as a team.
    * Please state the reason why the library is needed (timeline, complexity or benefits from adding a library that is an industry standard and will be useful elsewhere in Journey UI)
    * Include information about how much the vendor bundle size would be increased by the addition of the new library.  You can use the [Webpack Bundle Analyzer](https://github.com/webpack-contrib/webpack-bundle-analyzer) for this.
    * During this discussion, team members should be able to add their past experiences using the library and/or make alternate recommendation.
    * When adding the library to the project's `package.json` file, make sure to use an exact version number or use `~` with the version number instead of `^`.
* Once the library has been informally approved in the previous step, add all of the relevant information (bundle size addition, etc.) to the relevant JIRA ticket.
* Be sure to try a Webpack build to confirm that the new module will build properly using `npm run build`.


