## Angular.js
[Angular basics](https://angularjs.org/)

# Directory setup
* controllers
* services
* directives
* `app.js`

#app.js
The `app.js` file is the entry of the frontend framework.
It contains a variable pointing to a new module - `angular.module`.
The module contains the name of the app, and different components.

#index.html
Within the body tag, we add an attribute `ng-app` which is a *directive*.
(assuming the ng-[entry var name] = "[name of the app]" e.g. ng-app = "MyApp").
The body tag becomes the *scope* of the application.

Within a _nested_ div, we add an attribute `ng-controller` (assuming the nesting matters) a nested *directive*
This means properties attached to $scope in the controller are available in this div through an *expression* e.g. `{{ titile }}`


#MainController.js
We create a new controller to manage the app's data
```
[entry var name].controller([name], [$scope, function($scope)])

e.g.
app.controller('MainController', ['$scope', function($scope) {
  $scope.title = 'Top Sellers in Books';
}]);
```

The function allows you to add attributes to html elements and access them using `{{ title }}`
This is called an *expression*.

#expressions
$scope can hold expressions that are integers, strings or json objects.
To modify the format of an expression, pipe it into a filter e.g.
`{{ product.price | currency }}`
Other filters include
* date
* uppercase/lowercase
* filter
* orderBy (e.g. orderBy:'country')
* number
* json
* limitTo
[more on filters](https://docs.angularjs.org/api/ng/filter)

## Directives
Bind behavior to HTML elements.  When the app runs AngularJS walks through the HTML elements looking for directives `ng-`.  When one is found, it's behavior is triggered (like attaching scope `ng-app` or looping through an array `ng-repeat`)
Directives are a powerful way to create self-contained, interactive components. Unlike jQuery which adds interactivity as a layer on top of HTML, AngularJS treats interactivity as a native component of HTML.
* `ng-app`
* `ng-controller`
* `ng-repeat` - loops through array and displays each element
* `ng-src` - in place of `img src`
* `ng-click` - takes a function name as a string, passing in $index?
    `ng-click` can modify scope directly from the controller or through a custom directive link
* `ng-view` - is replaces with routes template
* `app-info` - custom directive

## Overview
* *Module* contains the different components of an AngularJS app
* *Controller* manages the app's data
* *Expression* displays values on the page
* *Filter* formats the value of an expression

# Views
Present the app's data through *expressions*, *directives* and *filters*.
Automatically updates changes from controller.

#UI
Users click on elements. If they have a directive, AngularJS runs the function.

#Controller
Updates the state of the data

### Directive Files
To remove redundancy in HTML code, make directives in the directives folder
* `appInfo.js`
Builds an `app.directive` that takes two arguments:
  * name - will be the name of the HTML element, name or class
  * function - returns three options
    * restrict - specifies how the directive will be used in the view
      `A` - only matches attribute name
      `E` - only matches element name
      `C` - only matches class name
      restrictions can be combined `AEC`
    * scope - specifies that we will pass info into the directive (isolated scope)
      `=` tells the directive to look for `info` in the `<app-info>`
      `&` [Lukea's isolated scope blog post](http://onehungrymind.com/angularjs-sticky-notes-pt-2-isolated-scope/)
      `@` [dnc253's explanation of @ and =](http://stackoverflow.com/questions/13032621/need-some-examples-of-binding-attributes-in-custom-angularjs-tags/13033249#13033249)
      [more resources](http://stackoverflow.com/questions/14050195/angularjs-what-is-the-difference-between-and-in-directive-scope)
    * templateUrl - specifies the HTML template to display the scope.info
    * link - function for a link to invoke
```
app.directive('appInfo', function() {
	return {
  	restrict: 'E',
    scope: {
      info: '='
    },
    templateUrl: 'js/directives/appInfo.html'
  };
  ---
  link: function(scope, element, attrs) {
  scope.buttonText = "Install",
  scope.installed = false,

  scope.download = function() {
    element.toggleClass('btn-active');
    if(scope.installed) {
      scope.buttonText = "Install";
      scope.installed = false;
    } else {
      scope.buttonText = "Uninstall";
      scope.installed = true;
    }
  }
}
});
```

*More on the link option*
  takes three arguments
  * scope - so you can add more attritubes to the $scope
  * element = the directive's HTML element
  * attrs contains the elements attributes

* `appInfo.html`
Holds the HTML format with `info` in place of the object
* `index.html`
Create a tag with the directives name and the `info` attribute.
`<app-info info="move"></app-info>`

*Why make your own directives?*
* Readability- expressive HTML
* Reusability- create self containing units of functionality

Combining `ng-repeat` and custom directives
```
<div class="container">
    <div class="card" ng-repeat="app in apps">
			<app-info info="app"></app-info>
    </div>
  </div>
</div>
```

# Services
`app.factory` creates a new service. `factory` takes two arguments:
* service name
* dependencies - array containing built-in `$http` and `function($http)`
  `$http` has methods `get`, `succes` and `error.`

In the `MainController`, add the service (by name) as a dependency.  From within the controller, call `[service name].success(funct)` to modify the `$scope`

*Why are services useful?*
 Instead of filling the controller with code to fetch weather data from a server, it's better to move this independent logic into a service so that it can be reused by other parts of the app.

*What can we generalize so far?*

Directives are a way to make standalone UI components, like `<app-info>`
Services are a way to make standalone communication logic, like `forecast` which fetches weather data from a server

# Routing
In `app.js` call `app.config(function($routeProvider))`. AngularJS provides `$routeProvider`to define the app routes.
`$routeProvider` has methods `when(url, options)` and `otherwise(options)`
  * options are `controller`, `templateUrl` or `redirectTo`

e.g.
```app.config(function($routeProvider) {
	$routeProvider
  	.when('/', {
    	controller: 'HomeController',
      templateUrl: 'views/home.html'
    })
  	.otherwise({
    	redirectTo: '/'
    });
});
```
In the example, the `HomeController` uses the `photo` service to retrieve json, which is used by the `home.html` template 
