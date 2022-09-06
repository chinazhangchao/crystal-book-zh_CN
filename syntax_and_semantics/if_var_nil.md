# if var.nil?

如果 `if`的条件是 `var.nil?` ，那么在 `then` 分支中，`var`的值就确定是 `Nil`，并且在`else`分支中它就确定不是 `Nil`：

```crystal
a = some_condition ? nil : 3
if a.nil?
  # 此处 a 是 Nil
else
  # 此处 a 是 Int32
end
```
