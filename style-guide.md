# Angular style guide

This isn't a comprehensive style guide. I would recommend using [John Papa's style guide](https://github.com/johnpapa/angular-styleguide) as a better starting point. I personally have some different styles than John's, mainly due to using ES6 or Typescript and less experience with the platform.

Sometimes I show method syntax or class syntax, sometimes both. Class syntax is preferred but sometimes method syntax has less friction and can be easier to control `this` (by closing over `self`). I don't see a problem with mixing both styles in the one project.


## Table of contents

1. [Class syntax](#class-syntax)
1. [Method syntax](#method-syntax)
1. [Scope](#scope)


## Class syntax


### Control `this`

Control `this`, either using `angular.bind` or by using an arrow function.

_Why?_ Callback function may be called in unexpected contexts, for example given `$scope.$watch(() => this.waitTimes, this.refresh)`, in `refresh()` the `this` value is `$scope`, not the containing class instance.

_Why?_ Incorrect management of `this` can result in code that is hard to work and debug.

_Why?_ Use `angular.bind` instead of `Function.bind` as it may not be not be available in older browsers.

_Why?_ Arrow functions are preferred to `angular.bind` because the syntax is more intuitive.


##### Avoid

```
class Dashboard {
	constructor($scope) {
		$scope.$watch(() => this.waitTimes, this.refresh, true);
	}

	function refresh() {
		this.waitTimes;	// does not exist
	}
}
Dashboard.$inject = ['$scope'];

angular
	.module('app')
	.directive(() => ({
		scope: {
			waitTimes: '='
		},
		bindToController: true,
		controller: Dashboard,
		controllerAs: 'vm'
	}))
```


##### Better

```
class Dashboard {
	constructor($scope) {
		$scope.$watch(() => this.waitTimes, angular.bind(this, this.refresh), true);
	}

	function refresh() {
		this.waitTimes;	// exists
	}
}
Dashboard.$inject = ['$scope'];

angular
	.module('app')
	.directive(() => ({
		scope: {
			waitTimes: '='
		},
		bindToController: true,
		controller: Dashboard,
		controllerAs: 'vm'
	}))
```


##### Prefer

```
class Dashboard {
	constructor($scope) {
		$scope.watch(() => this.waitTimes, () => this.refresh(), true);
	}

	function refresh() {
		this.waitTimes;	// exists
	}
}
Dashboard.$inject = ['$scope'];

angular
	.module('app')
	.directive(() => ({
		scope: {
			waitTimes: '='
		},
		bindToController: true,
		controller: Dashboard,
		controllerAs: 'vm'
	}))
```

## Method syntax


### Control `this`

Control `this`, either with fat arrow syntax, or by closing over `self` instead of relying on `this` or using `bind`.

_Why?_ Bind syntax can be confusing, as the changing value of `this` can lead to logic errors.

_Why?_ Closing over a known value of `this` makes the resulting code easier to read and maintain.


##### Avoid

```
function Dashboard($scope) {
	$scope.$watch(() => this.waitTimes, refresh);

	function refresh() {
		this.waitTimes;	// does not exist
	}
}
Dashboard.$inject = ['$scope'];

angular
	.module('app')
	.directive(() => ({
		scope: {
			waitTimes: '='
		},
		bindToController: true,
		controller: Dashboard,
		controllerAs: 'vm'
	}))
```


##### Better

```
function Dashboard($scope) {
	$scope.$watch(() => this.waitTimes, () => refresh());

	function refresh() {
		this.waitTimes;	// exists
	}
}
Dashboard.$inject = ['$scope'];

angular
	.module('app')
	.directive(() => ({
		scope: {
			waitTimes: '='
		},
		bindToController: true,
		controller: Dashboard,
		controllerAs: 'vm'
	}))
```


##### Prefer

```
function Dashboard($scope) {
	var self = this;

	$scope.$watch(() => self.waitTimes, refresh);

	function refresh() {
		self.waitTimes;	// exists
	}
}
Dashboard.$inject = ['$scope'];

angular
	.module('app')
	.directive(() => ({
		scope: {
			waitTimes: '='
		},
		bindToController: true,
		controller: Dashboard,
		controllerAs: 'vm'
	}))
```


## Scope


### Watching the scope

Items on the scope can be watched using `$scope.$watch`.


#### Callbacks for targets

Use a callback for the watch expression instead of a string.

_Why?_ A string expression cannot be checked by a preprocessor so it is prone to errors and isn't friendly when renaming and refactoring.

_Why?_ A string expression can only watch public values from the scope.

_Why?_ Using `controllerAs` syntax means that the controller is nested in the scope, so the watch expression must be something like `'vm.valueToWatch'`. This ties the name used for the controller to the controller method itself.

_Why?_ Callbacks for the watch expression can perform calculations and be more complex than string expressions


##### Avoid

```
class Dashboard {
	constructor($scope) {
		$scope.$watch('vm.waitTimes', () => {
			// ...
		});
	}
}
Dashboard.$inject = ['$scope'];

angular
	.module('app')
	.directive(() => ({
		scope: {
			waitTimes: '='
		},
		bindToController: true,
		controller: Dashboard,
		controllerAs: 'vm'
	}))
```


##### Prefer

```
class Dashboard {
	constructor($scope) {
		$scope.watch(
			() => this.waitTimes, 
			() => {
				// ...
			});
	}
}
Dashboard.$inject = ['$scope'];

angular
	.module('app')
	.directive(() => ({
		scope: {
			waitTimes: '='
		},
		bindToController: true,
		controller: Dashboard,
		controllerAs: 'vm'
	}))
```

