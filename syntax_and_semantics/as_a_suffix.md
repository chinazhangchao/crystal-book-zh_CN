# 作为后缀

`if` 可以写在表达式后，作为后缀：

```crystal
a = 2 if 某些条件

# 上式等同于：
if 某些条件
  a = 2
end
```

有时这会使代码看起来自然。
