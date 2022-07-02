# 元组(Tuple)

[元组](http://crystal-lang.org/api/Tuple.html) 通常以元组字面量创建：

```crystal
tuple = {1, "hello", 'x'} # Tuple(Int32, String, Char)
tuple[0]                  #=> 1       (Int32)
tuple[1]                  #=> "hello" (String)
tuple[2]                  #=> 'x'     (Char)
```

[Tuple.new](http://crystal-lang.org/api/Tuple.html#new%28%2Aargs%29-class-method)可以创建空元组。

以下方法可以声明一个元组类型：

```crystal
# 这表示包含 Int32, String 和 Char 的元组。
Tuple(Int32, String, Char)
```

在类型声明，泛型参数和其他需要类型的地方，你可以用简便的语法，详见[类型语法](../type_grammar.html):

```crystal
# An array of tuples of Int32, String and Char
Array({Int32, String, Char})
```
