# as?

`as?`伪方法类似于 `as`,除了当类型不匹配时它返回 `nil`，而不是抛出异常。它也可以用于转换指针类型和其他类型。

例如：

```crystal
value = rand < 0.5 ? -3 : nil
result = value.as?(Int32) || 10

value.as?(Int32).try &.abs
```
