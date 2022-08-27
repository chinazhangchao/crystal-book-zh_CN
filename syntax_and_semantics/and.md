# && - 逻辑与运算符

`&&` (与) 先求其左式的值。如果左边*是真的*，它的值就是其右边的值。否则它的值就是左式的值(多半是 `false` 或`nil`)。他的类型是左右两边类型的或。

你可以认为 `&&` 是 `if`的语法糖：

```crystal
some_exp1 && some_exp2

#上式等同于:
tmp = some_exp1
if tmp
  some_exp2
else
  tmp
end
```
