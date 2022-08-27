# alias : 类型别名

可以用 `alias` 给类型赋予别名：

```crystal
alias PInt32 = Pointer(Int32)

ptr = PInt32.malloc(1) # : Pointer(Int32)
```

编辑器会把每个 alias 替换成其指代的类型。

别名使你得以避免写超长类型名，也可以用于描述递归类型：

```crystal
alias RecArray = Array(Int32) | Array(RecArray)

ary = [] of RecArray
ary.push [1, 2, 3]
ary.push ary
ary #=> [[1, 2, 3], [...]]
```

现实生活中一个递归类型的例子是 json:

```crystal
module Json
  alias Type = Nil |
               Bool |
               Int64 |
               Float64 |
               String |
               Array(Type) |
               Hash(String, Type)
end
```
