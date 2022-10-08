# is_a?

伪方法 `is_a?` 确定了这个变量的运行时类型是否继承或包含其他的类型。例如：

```crystal
a = 1
a.is_a?(Int32)          #=> true
a.is_a?(String)         #=> false
a.is_a?(Number)         #=> true
a.is_a?(Int32 | String) #=> true
```

这是一个伪方法，，因为编译器认识它，且它会改变变量的类型信息，详见 [if var.is_a?(...)](if_varis_a.html)。另外，它接受一个 [类型](type_grammar.html)，这必须在编译时确定。
