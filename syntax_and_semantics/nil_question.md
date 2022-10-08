# nil?

伪方法 `nil?` 确定一个变量的运行时类型是不是 `Nil`。例如：

```crystal
a = 1
a.nil?          # => false

b = nil
b.nil?          # => true
```

这是个伪方法，因为编译器认识它，并且使用它会改变变量的类型信息，见 [if var.nil?(...)](if_var_nil.html)。

它和 `is_a?(Nil)`效果相同，但是更短，更容易读，写。
