# EXOS Front-End Code Standards and Practices

## General Rules
Please use the included [`.editorconfig`](.editorconfig) file to enforce the following rules for all files.

 * Use spaces for tabs
 * Tab size is 4 spaces, except for `.json` files which is 2 spaces.
 * No trailing whitespace, except for Markdown files (`.md`).
 * Add a new line to the end of files.


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

### New Lines
All tags and content should be surrounded by new blank lines.

```html
<div class="content">

    <div class="container">

        <div class="row">

            <div class="col-md-12">

               Foo

            </div>

        </div>

    </div>

</div>
```

The exception is if an element tag has no attributes and it's child content is text or template variable that does not cause the total line count to exceed 100 characters.

**Good**
```html
<h3>Paths by Focus Area</h3>
```

**Bad**
```html
<h3 class="content-title text-uppercase">Paths by Focus Area</h3>
```

### Self Closing Tags
In HTML5 the ending slash is optional in self-closing tags. We will not include the ending slash.

```html
<input type="text" name="name" value="John">

<br>
```

A complete list of self-closing tags (aka void elements) can be [found here](http://w3c.github.io/html/syntax.html#void-elements).


### Template Variables
Always include spaces between curly braces.
```html
{{ Controller.variable }}
```

## JavaScript

### General Rules
 * Always use single quotes
 * Use camelCase for all variables
 * The ? and : in a ternary conditional must have space on both sides.
 * `if` / `else` / `for` / `while` / `try` blocks should always use braces and always go on multiple lines.
 * Always include a new line between a function declaration and the opening parenthesis. 
 * No space before opening parenthesis in function declaration.
 
```javascript
if (isEnabled) {
    ...
}

var myFunc = function(arg) {
    ...
};
```

### Guidelines
 * Lines should not exceed 100 characters.

### Arrays

Arrays with single items can be defined on a single line.
```javascript
var arr = ['foo'];
```

Arrays with multiple items should be defined on multiple lines.
```javascript
var arr = [
    'foo',
    'bar'
];
```

### Array of Objects
The following format is preferred when creating an array of objects.
```javascript

var objects = [{
    firstName: 'John',
    lastName: 'Tavares'
}, {
    firstName: 'Ryan',
    lastName: 'Strome'
}, {
    firstName: 'Frans',
    lastName: 'Nielsen'
}];

```

## Angular

### IIFE
Wrap components in an immediately-invoked function expression (IIFE), passing in necessary global objects as parameters. Include the `'use strict'` expression to force strict mode.

```javascript
(function(angular) {
    'use strict';

    var module = angular.module('WebApp');
    
}(angular));
```

### Component Structure
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

### Dependency Injection

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

### Routes ([ui-router](http://angular-ui.github.io/ui-router/site/#/api/ui.router))

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



### Services

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

### Controllers

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

### Directives

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

## CSS ([SCSS](http://sass-lang.com/))

### General Rules

 * All properties should be alphabatized.
 * Use shorthand when possible.
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


## Code Reviews ([Crucible](https://crucible.teamexos.com/cru/browse/EFE))
 
 * A code review should be opened for all tickets except where the code change is trivial.
 * Code reviews can be opened by appending `+EFE` to the end of a commit message or through JIRA.
 * Add any new code changes to an existing review instead of opening a new review.
 * Remove dist/build files from reviews.


### Closing Reviews
* A review can only be closed when all review comments have been addressed and at least two front-end developers have marked the review as complete.
* The front-end lead is responsibile for closing reviews in their respective projects.

