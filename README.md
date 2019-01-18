# go-tagexpr

An interesting go struct tag expression syntax for field validation, etc.

## Status

In development

## Example

```go
type T struct {
	A int              `tagexpr:"$<0||$>=100"`
	B string           `tagexpr:"len($)>1 || regexp('^\\w*$', $)"`
	C bool             `tagexpr:"{expr1:(G)$['J']>0 && $}{expr2:'C must be true when T.G.J>0'}"`
	D []string         `tagexpr:"{expr1:len($)>0 && $[0]=='D'} {expr2:sprintf('Invalid D:%s',$)}"`
	E map[string]int   `tagexpr:"{expr1:$k!='' && $v>0}{expr2:$['E']>0}"`
	F map[string][]int `tagexpr:"$$v>0 && len($['F'])>0 && $['F'][0]>1"`
	G struct{ J int }
}
```

## Syntax

Struct tag syntax spec:

```
type T struct {
    Field1 T1 `tagName:"expression"`
    Field2 T2 `tagName:"{exprName:expression} [{exprName2:expression2}]..."`
    ...
}
```

NOTE: **The `exprName` under the same struct field cannot be the same！**

|Operator or Expression example|Explain|
|-----|---------|
|`+`|Digital addition or string splicing|
|`-`|Digital subtraction|
|`*`|Digital multiplication|
|`/`|Digital division|
|`%`|Digital division remainder|
|`&`|Integer bitwise `and`|
|`\|`|Integer bitwise `or`|
|`^`|Integer bitwise `not` or `xor`|
|`&^`|Integer bitwise `clean`|
|`<<`|Integer bitwise `move left`|
|`>>`|Integer bitwise `move right`|
|`==`|`eq`|
|`!=`|`ne`|
|`>`|`gt`|
|`>=`|`ge`|
|`<`|`lt`|
|`<=`|`le`|
|`&&`|Logic `and`|
|`\|\|`|Logic `or`|
|`()`|Expression group|
|`0`|Digital "0"|
|`'S'`|String "S"|
|`(X)$`|Struct field named X|
|`$`|Shorthand for `(X)$`, omit `(X)` to indicate current struct field|
|`(X)$['A']`|Map or struct field value with name A in the struct field X|
|`(X)$[0]`|The 0th element of the struct field X(type: map, slice, array)|
|`(X)$$`|Traverse each element of the struct field X(type: map, slice, array)|
|`(X)$k`|Traverse each element key of the struct field X(type: map, slice, array)|
|`(X)$v`|Traverse each element value of the struct field X(type: map, slice, array)|
|`len((X)$)`|Built-in function `len`, the length of struct field X|
|`sprintf('X value: %v', (X)$)`|`fmt.Sprintf`, format the value of struct field X|
|`regexp('^\\w*$', (X)$)`|Regular match the struct field X, return boolean|