# 作为表达式

`if` 的值是每一个分支的最后一个表达式的值：

```crystal
a = if 2 > 1
      3
    else
      4
    end
a #=> 3
```

如果 `if` 的一个分支是空的，或没有这个分支，那这个分支的值默认是 `nil`：

```crystal
if 1 > 2
  3
end

# 上式等同于：
if 1 > 2
  3
else
  nil
end

# 另一个例子
if 1 > 2
else
  3
end

# 上式等同于：
if 1 > 2
  nil
else
  3
end
```
