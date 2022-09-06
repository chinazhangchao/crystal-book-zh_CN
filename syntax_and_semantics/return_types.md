# 返回类型

编译器可以推出来一个方法的返回类型。但是因为以下两个原因，你应当手动确定它：

1. 保证这个方法返回了你想要的类型
2. 让它在帮助文档中出现

例如：

```crystal
def some_method : String
  "hello"
end
```

返回类型也遵循[类型语法](type_grammar.html).

## Nil 返回类型

标志一个方法返回 `Nil` 会让他把任何实际返回的值都丢弃，只剩一个 `nil` 。

```crystal
def some_method : Nil
  1 + 2
end

some_method # => nil
```

这在两种情况下很有用：

1. 保证这个方法返回 `nil` 而不用在每个返回的地点都加一个 `nil`
2. 在文档中指出：这个方法的返回值没有意义

这些方法的返回值没有意义，因此它们通过副作用发挥功能。

用 `Void` 类型标识可以起到同样的效果，但是 `Nil` 更加理想。 `Void` 适合在 C 绑定中使用。

## NoReturn 返回类型

一些表达式不可能回到当前的作用域，因此它们根本不会返回(区别于上面有返回值但是没意义的情况)这种表达式用特殊的返回类型`NoReturn`表示。

典型的无返回值的方法和关键词有 `return`，`exit`，`raise`，`next`，和 `break`。

这常用于解构联合类型：

```
string = STDIN.gets
typeof(string)                        # => String?
typeof(raise "Empty input")           # => NoReturn
typeof(string || raise "Empty input") # => String
```

编译器会知道当变量 `string`是 `Nil`类型时，表达式 `string || raise`的右边会被求值。因为 `typeof(raise "Empty input")` 是 `NoReturn`，这个表达式执行起来根本不会到达当前的作用域。所以表达式求值剩下的类型就是 `String`。

如果一个表达式所有的可能都导致 `NoReturn` ，它自己也会是 `NoReturn`类型。 `NoReturn`不会在联合类型中出现，因为它实际上寓于所有类型之中。只有要确认当前表达式不会返回，这个类型才会被写出来。

> 译注：从类型代数的角度说，它是所有类型的[始对象]()，拥有从他产生任何类型的能力(在程序中也许是rescue?)，但是它不能从任何类型构造。这种类型在C/C++中用`[[noreturn]]`属性标识，在Haskell中称为`Void`。对应的，`Nil` 被称为[终对象](https://encyclopediaofmath.org/wiki/Final_object)，可以从任何类型构造它，这类似于函数式语言中的`unit`或`()`。 更详细的内容可以参考 [给程序员的范畴论](https://github.com/hmemcpy/milewski-ctfp-pdf)

`NoReturn` 可以显式地设为方法或函数的返回类型，不过往往也能被编译器推出来。
