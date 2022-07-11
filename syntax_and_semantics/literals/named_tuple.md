# 命名元组(NamedTuple(

[命名元组](http://crystal-lang.org/api/NamedTuple.html)往往由元组字面量产生：

```crystal
tuple = {name: "Crystal", year: 2011} # NamedTuple(name: String, year: Int32)
tuple[:name] # => "Crystal" (String)
tuple[:year] # => 2011      (Int32)
```

可以用如下方法声明一个命名元组：

```crystal
# 这个表示命名元组 x: Int32, y: String 的类型：
NamedTuple(x: Int32, y: String)
```

在类型限制，泛型参数或其他需要类型的地方，你可以用更简短的语法，见[类型语法](../type_grammar.html):

```crystal
# 元组 {x: Int32, y: String}构成的数组
Array({x: Int32, y: String})
```

元组的键也可以是字符串字面量:

```crystal
{"这是键": 1}
```
