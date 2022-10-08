# || - 逻辑或运算符

`||` (或) 先求左边表达式的值，如果他是 *假的*， 它就会求值右边的表达式，并把自己的值设为它。它的类型是左右两边类型的并。

你可以认为 `||` 是 `if`的语法糖：

```crystal
some_exp1 || some_exp2

# 上式等同于:
tmp = some_exp1
if tmp
  tmp
else
  some_exp2
end
```
