This fix tests the handling of arrays in catmandu.

`catmandu convert JSON --fix '/home/tobias/git/Catmandu-Testing/ArrayHandling/test.fix' < '/home/tobias/git/Catmandu-Testing/ArrayHandling/test.json' | jq`

`copy_field` with_
	- Arrays of Strings with multiple values
	- Array of String with one value
	- Array without value