# unless

与`if`相反，`unless` 当条件为 *假* 的时候求值then分支，分则求值else分支(如果有)：

```crystal
unless some_condition
  then_expression
else
  else_expression
end

# 上式等同于:
if some_condition
  else_expression
else
  then_expression
end

# 也可以后置
close_door unless door_closed?
```
