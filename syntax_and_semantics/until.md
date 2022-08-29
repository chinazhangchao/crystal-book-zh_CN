# until

`until`执行它的循环体，直到条件是 *真的*。 一个 `until` 只是 `while`否定格式的一个语法糖：

```crystal
until some_condition
  do_this
end

# 上式等价于:
while !some_condition
  do_this
end
```

`break` 和 `next` 也可以在 `until`里使用。
