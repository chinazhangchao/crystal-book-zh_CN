# while

`while`表达式只要条件是*真*的,就执行它的内容。

```crystal
while some_condition
  do_this
end
```

`while`先测试条件，如果为 *真*，就执行主体。即，也许主体根本不会被执行(如果条件一开始就是假的)。 

`while`的类型永远是 `Nil`.

类似于 `if`,如果 `while`的条件是一个变量，这个变量在循环体中保证不是`nil`。如果条件是测试 `var.is_a?(Type)`， `var` 就保证是 `Type`的那个类型。如果条件是 `var.responds_to?(:method)`， `var`就保证能响应这个方法。

`while` 之后，一个变量的类型取决于他在 `while` 之前的值，以及 `while` 中对他的最后一次赋值：

```crystal
a = 1
while some_condition
  # a : Int32 | String
  a = "hello"
  # a : String
  a.size
end
# a : Int32 | String
```

## 在循环结尾检查条件

如果你至少要执行一次循环体，然后检查是否跳出，你可以这么做：

```crystal
while true
  do_something
  break if some_condition
end
```

或者使用标准库里的 `loop`：

```crystal
loop do
  do_something
  break if some_condition
end
```
