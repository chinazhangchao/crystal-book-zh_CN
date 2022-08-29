# break

你可以使用 `break` 跳出`while` 循环：

```crystal
a = 2
while (a += 1) < 20
  if a == 10
    break # 跳到 'puts a'
  end
end
puts a #=> 10
```

`break` 也可以带一个参数，这会在跳出`while`的时候返回一个值：

```crystal
def foo
  loop do
    break "bar"
  end
end
puts foo #=> "bar"
```
