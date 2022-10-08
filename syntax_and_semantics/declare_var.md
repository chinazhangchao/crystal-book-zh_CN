# 声明未初始化的变量

Crystal 允许你声明变量，而不初始化它：

```crystal
x = uninitialized Int32
x #=> 一些随机而无用的信息，不要假设它有任何行为。
```

这是[不安全的](unsafe.html)代码，并且总是用于底层代码的适配。最常见的例子是声明一个未初始化的 [StaticArray](http://crystal-lang.org/api/StaticArray.html) (静态数组) 做缓冲区，而不必担心性能开销：

```crystal
buffer = uninitialized UInt8[256]
```

这个缓冲区会在栈上分配，不会分配在堆上。

`uninitialized` 关键字之后的类型应符合 [类型语法](type_grammar.html)。

