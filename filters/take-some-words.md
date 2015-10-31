# Take Some Words

Trims a string input to a provided number of words and append an ellipsis (...). If the input has less than the provided number of words, just return the input.


## Code
```
(() => {
	angular
		.module('app')
		.filter('takeSomeWords', [
			() => {
				return (input, numberOfWords) => {
					var words = input.split(' ');

					if (words.length <= numberOfWords) {
						return input;
					}

					var summary = words.slice(0, numberOfWords).join(' ')

					return `${summary}...`;
				};
			}
		]);
})();
```


## Usage

```
$scope.howl = "Peyote solidities of halls, backyard green tree cemetery dawns, wine drunkenness over the rooftops, storefront boroughs of teahead joyride neon blinking traffic light, sun and moon and tree vibrations in the roaring winter dusks of Brooklyn, ashcan rantings and kind king light of mind";

<p>{{howl | takeSomeWords:10}}</p>

// result:
Peyote solidities of halls, backyard green tree cemetery dawns, wine...
```

