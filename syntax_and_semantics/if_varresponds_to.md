# if var.responds_to?(...)

如果 `if`的条件是 `responds_to?`测试，那么在 `then` 分支该变量保证响应这种方法：

```crystal
if a.responds_to?(:abs)
  # 这里a的确定可以响应 'abs' 方法
end
```

另外，在 `else` 分支该变量保证不响应该方法：

```crystal
a = some_condition ? 1 : "hello"
# a : Int32 | String

if a.responds_to?(:abs)
  # 此处 a 会是 Int32, 因为 Int32#abs 存在, 而  String#abs 不存在
else
  # 此处 a 会是 String
end
```
# 限制

上述规则 **不适用于** 成员变量或类变量。为了在这些地方使用它，首先把它赋值给一个局部变量：

```crystal
if @a.responds_to?(:abs)
  # 此处 @a 不能保证接收 `abs` 消息
end

a = @a
if a.responds_to?(:abs)
  # 此处 a 保证接收 `abs` 消息
end

# A bit shorter:
if (a = @a).responds_to?(:abs)
  # 此处 a 保证接收 `abs` 消息
end
```

