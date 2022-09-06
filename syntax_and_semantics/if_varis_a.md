# if var.is_a?(...)

如果 `if`的条件是 `is_a?` 测试，那`then`分支中该表达式就确定是这个类型。 

```crystal
if a.is_a?(String)
  # 此处 a 是 String
end

if b.is_a?(Number)
  # 此处 b 是 Number
end
```

另外，在 `else` 分支中该变量就确定不是这个类型：

```crystal
a = some_condition ? 1 : "hello"
# a : Int32 | String

if a.is_a?(Number)
  # a : Int32
else
  # a : String
end
```

注意，你可以用 `is_a?`测试任意的类型，比如抽象类和模块。

上述规则也适用于条件中的与运算(`&&`)：

```crystal
if a.is_a?(String) && b.is_a?(Number)
  # 此处 a 是 String , b 是 Number
end
```
## 限制

上述规则 **不适用于** 成员变量或类变量。为了在这些地方使用它，首先把它赋值给一个局部变量：

```crystal
if @a.is_a?(String)
  # 此处 @a 不能保证是 String
end

a = @a
if a.is_a?(String)
  # 此处 a is 保证是 String
end

# A bit shorter:
if (a = @a).is_a?(String)
  # 此处 a is 保证是 String
end
```
