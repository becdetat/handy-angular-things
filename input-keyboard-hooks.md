# Input keyboard hooks

<kbd>Enter</kbd> and <kbd>Escape</kbd> hooks for inputs. So you could have an `<input>` and add a `enter-pressed` attribute to wire up an event to the user pressing <kbd>Enter</kbd>.


## Code
	(() => {
		function getHandler(key, scope, el, toApply) {
			el.bind('keyup', evt => handleKeyup(key, evt.which, scope, toApply));
		}
		function handleKeyup(key, which, scope, toApply) {
			if (key == which) {
				scope.$apply(toApply);
			}
		}

		angular
			.module('handyAngularThings')
			.directive('enterPressed', () => {
				return (scope, el, attrs) => getHandler(13, scope, el, attrs.enterPressed);
			})
			.directive('escapePressed', () => {
				return (scope, el, attrs) => getHandler(27, scope, el, attrs.escapePressed);
			});
	})();


## Usage

	<input type="text"
		   ng-model="vm.textValue"
		   enter-pressed="vm.apply()"
		   escape-pressed="vm.cancel()" />