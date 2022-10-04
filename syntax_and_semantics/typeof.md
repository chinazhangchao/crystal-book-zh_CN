# typeof

`typeof`表达式返回原表达式的类型：

```crystal
a = 1
b = typeof(a) #=> Int32
```

它接受多个参数，返回的结果是这些参数类型的并：

```crystal
typeof(1, "a", 'a') #=> (Int32 | String | Char)
```

这常用于泛型代码，来利用编译器的类型推导功能：

```crystal
hash = {} of Int32 => String
another_hash = typeof(hash).new #:: Hash(Int32, String)
```

因为`typeof` 并不事实上对参数表达式求值，它可以用于编译时的方法。在这个例子中，它被用于从嵌套的类型参数中递归地构建出一个联合类型：

```crystal
class Array
  def self.elem_type(typ)
    if typ.is_a?(Array)
      elem_type(typ.first)
    else
      typ
    end
  end
end

nest = [1, ["b", [:c, ['d']]]]
flat = Array(typeof(Array.elem_type(nest))).new
typeof(nest) #=> Array(Int32 | Array(String | Array(Symbol | Array(Char))))
typeof(flat) #=> Array(String | Int32 | Symbol | Char)
```

这个表达式也可用于[类型语法](type_grammar.html)。
