# next

可以用 `next`直接跳到执行`while`的下一个循环。在 `next`执行后， `while`会检查条件，如果是 *真*的，它就接着执行循环体。

```crystal
a = 1
while a < 5
  a += 1
  if a == 3
    next
  end
  puts a
end
# 上面的结构打印数 2, 4 和 5
```
