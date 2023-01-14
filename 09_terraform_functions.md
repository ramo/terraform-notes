## Terraform buit-in functions
- Terraform supports only buit-in functions, no support for user-defined functions.
- Terraform console `terraform console` can be used to experiment these funtions.

### Numeric Functions
#### max `max(number...)`
```
> max(12, 54, 3)
54
> max([12, 54, 3]...)
54
```
#### min `min(number...)`
```
> min(12, 54, 3)
3
> min([12, 54, 3]...)
3
```
#### ceil `ceil(number)`
```
> ceil(5)
5
> ceil(5.1)
6
```
#### floor `floor(number)`
```
> floor(5)
5
> floor(5.1)
5
```

### String Functions
#### split `split(separator, string)`
```
> split(",", "foo,bar,baz")
["foo", "bar", "baz"]
```
#### join (`Opposite to split`) `join(separator, list)`
```
> join(",", ["foo", "bar", "baz"])
foo,bar,baz
```
#### substr `substr(string, offset, length)`
```
> substr("hello world", 1, 4)
ello
```
#### lower `lower(string)`
```
> lower("HELLO")
hello
```
#### upper `upper(string)`
```
> upper("hello")
HELLO
```
#### title `title(string)`
```
> title("how are you")
How Are You
```
### Collection Functions
#### length `length(list|map|string)`
- string - number of characters and not bytes.
- map|list - number of elements in that collection.
#### index `index(list, value)`
```
> index(["a", "b", "c"], "b")
1
```
#### element `element(list, index)`
- zero based index.
- index is non-negative.
- error if used with empty array.
- built-in index syntax `list[index]` should be used in most cases.
- best suitable for its **wrap around** behavior shown below.
```
# wrap around behavior
> element(["a", "b", "c"], 3)
a
```
#### contains `contains(list, value)`
#### keys `keys(map)` 
- returns list of keys in lexographical order.
#### values `values(map)`
- returns list of values in lexographical order of their keys.
#### lookup `lookup(map, key, default)`
- Omitting default value is deprecated from v0.7, because that would be equivalent to `map[key]`

### Filesystem Functions
#### file `file(path)`
- returns the content of the file as string.

### Type Conversion Functions
#### toset `toset(list)`
- Removes duplicates and returns set from the list.
