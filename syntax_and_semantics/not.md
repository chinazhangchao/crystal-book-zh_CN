# if !

`!` 运算符翻转一个变量的 [真性](truthy_and_falsey_values.html)，返回`Bool`.

当在 `if` 中与一个变量连接时， `is_a?`， `responds_to?` 或 `nil?` 都会相应地翻转自己的结论：

```crystal
a = some_condition ? nil : 3
if !a
  # 此处 a 是 Nil 因为 a 在这个分支是假的
else
  # 此处 a 是 Int32, 因为 a 在这个分支是真的
end
```

```crystal
b = some_condition ? 1 : "x"
if !b.is_a?(Int32)
  # 此处 b 是 String ，因为它不是 Int32
end
```
