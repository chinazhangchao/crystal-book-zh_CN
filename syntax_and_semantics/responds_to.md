# responds_to?

伪方法 `responds_to?` 确定一个类型是否响应某个方法。例如：

```crystal
a = 1
a.responds_to?(:abs)    #=> true
a.responds_to?(:size) #=> false
```

它是伪方法，因为它只接受符号字面量，并且别编译器特殊对待，见 [if var.responds_to?(...)](if_varresponds_to.html)。
