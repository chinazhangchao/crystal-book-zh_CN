# 符号(Symbol)

[符号](http://crystal-lang.org/api/Symbol.html) 表示整个源代码中独一无二的名字。

符号在编译时被处理，不能被动态创建。唯一能创建符号的方法是符号字面量，其以冒号(`:`)开头，接一个标识符。标识符可以用双引号(`"`)包括。

```crystal
:unquoted_symbol
:"quoted symbol"
:"a" # identical to :a
```

用双引号包括的字面量可以包含任意Unicode字符，转义类似于[字符串字面量](./string.html)，但没有插值。

没有引号的标识符命名规则类似于方法。它可以含有数字下划线，或是码点大于`159`(`0x9F`)的字符。它不能以数字开头，但可以以感叹号(`!`)或问号(`?`)结尾。 

```crystal
:question?
:exclamation!
```

所有的[Crystal运算符](../operators.html)可以不加引号地制成符号：
```crystal
:+
:-
:*
:/
:%
:&
:|
:^
:**
:>>
:<<
:==
:!=
:<
:<=
:>
:>=
:<=>
:===
:[]
:[]?
:[]=
:!
:~
:!~
:=~
```

在内部，符号被解析成 `Int32`类型的数字。

## 百分号符号数组字面量

除了单独的符号字面量，还有可以创建符号 [数组](https://crystal-lang.org/api/Array.html)的百分号字面量。它以 `%i` 开头，接一对分隔符。
可用的分隔符是圆括号`()`，方括号 `[]`，花括号`{}`，尖括号  `<>`和竖杠。除了竖杠，其他的分隔符都可以嵌套，在内部如同普通的字符。

```crystal
%i(foo bar baz) # => [:foo, :bar, :baz]
%i(foo\nbar baz) # => [:"foo\nbar", :baz]
%i(foo(bar) baz) # => [:"foo(bar)", :baz]
```

标识符可以含有Unicode字符。如果一个标识符内部有空格(` `)，那么它必须转义，否则会把标识符切开。

```crystal
%i(foo\ bar baz) # => [:"foo bar", :baz]
%i(foo bar baz)  # => [:foo, :bar, :baz]
```
