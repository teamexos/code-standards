
# AngularJS Coding Standards

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
