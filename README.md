# EXOS Front-End Code Standards and Practices

## General Rules
Please use the included [`.editorconfig`](.editorconfig) file to enforce the following rules for all files.

 * Use spaces for tabs
 * Tab size is 4 spaces, except for `.json` files which is 2 spaces
 * No trailing whitespace, except for Markdown files (`.md`)
 * Add a new line to the end of files



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
 * Lines should not exceed 100 characters
 * The ? and : in a ternary conditional must have space on both sides.
 * `if` / `else` / `for` / `while` / `try` blocks should always use braces and always go on multiple lines
 * Always include a new line between a function declaration and the opening parenthesis. 
 
```javascript
if (isEnabled) {
    ...
}

var myFunc = function (arg) {
    ...
};
```

## Angular

### IEFE
Wrap components in an immediately executing function expression (IEFE), passing in necessary global objects as parameters.

```javascript
(function (angular) {

    var module = angular.module('WebApp');
    
}(angular));
```

### Component Structure
 * New components should be as modular as possible.
 * Each component should be contained within it's own directory
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

Each component should be it's own Angular Module. The naming convention should be in UpperCamelCase with any parent modules separated with a period.

If the module has no dependencies an empty array on the same line should be used.
```javascript
var module = angular.module('ParentModule.ChildModule.GrandChildModule', []);
```

If the module has one or more dependencies they should be stacked on new lines like so.
```javascript
var module = angular.module('ParentModule.ChildModule.GrandChildModule', [
    'ui.router',
    'ui.bootstrap
]);
```

### Dependency Injection
All Angular modules should define their dependencies using array notation.
```javascript
module.controller('MyController', [
    'MyService',
    function (MyService) {
    ...
    }
]);
```

Modules with multiple dependencies should define each on a new line.
```javascript
module.controller('MyController', [
    'MyService',
    'YourService',
    function (
        MyService,
        YourService
    ) {
    ...
    }
]);
```

All modules prefixed with a dollar sign should come before all other modules.
```javascript
module.controller('MyController', [
    '$http',
    'MyService',
    'YourService',
    function (
        $http,
        MyService,
        YourService
    ) {
    ...
    }
]);
```

### Routes
Info about routes.

### Services
Names should be UpperCamelCase and contain the `Service` suffix.
`MyAppService`

Each service returns a public object, named after the service. 
```javascript
module.factory('MyService', [
    function () {
    
        var MyService = {};
        
        MyService.getDetails = function () {
        ...
        };
        
        return MyService;
    
    }
]);
```

### Controllers
Names should be UpperCamelCase and contain the `Controller` suffix. 
`MyAppController`

Controllers should be as slim as possible. Keep logic in Services and Directives.

Since we're using `controllerAs` in our routes we should avoid using `$scope` and bind to `this` via a `vm` variable.
```javascript
module.controller('MyController', [
    'MyService',
    function (MyService) {
        
        var vm = this;
        
        vm.firstName = MyService.details.firstName;
        
    }
]);
```

### Directives
Info about directives


## Code Reviews
