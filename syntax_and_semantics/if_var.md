# if 变量

如果一个变量被用作 `if` 的条件，那么在 `then`分支内他会被认为不可能是 `Nil`类型：

```crystal
a = some_condition ? nil : 3
# a is Int32 or Nil

if a
  # 既然我们只能通过 a 为真一种方式到达这里,
  # a 就不能是. 所以在此， a 是 Int32.
  a.abs
end
```

同样的思维用于当一个值在if表达式中被赋值时：

```crystal
if a = some_expression
  # 此处 a 不会是 nil
end
```

逻辑与运算符(`&&`)也适用此规律： 

```crystal
if a && b
  # 这里 a 和 b 保证都不是 Nil
end
```

这里， `&&` 表达式右边的 `b` 也保证不是 `Nil`。

当然，在 `then` 分支重赋值变量会导致变量有新的类型。

## 限制

这个逻辑 **不适用于** 成员变量，类变量和其他在闭包中绑定的变量。这些变量的值有可能在条件判断之后又被另外的纤程修改，导致它变成 `nil`。

```crystal
if @a
  # 此处 `@a` 可以是 nil
end

if @@a
  # 此处 `@@a` 可以是 nil
end

a = nil
closure = ->(){ foo = "foo" } # 这个闭包绑定了前面所有的变量，包括 a

if a
  # 此处 `a` 可以是 nil
end
```

This can be circumvented by assigning the value to a new local variable:

```crystal
if a = @a
  # here `a` can't be nil
end
```

Another option is to use [`Object#try`](https://crystal-lang.org/api/Object.html#try%28%26block%29-instance-method) found in the standard library which only executes the block if the value is not `nil`:

```crystal
@a.try do |a|
  # here `a` can't be nil
end
```

## Method calls

That logic also doesn't work with proc and method calls, including getters and properties, because nilable (or, more generally, union-typed) procs and methods aren't guaranteed to return the same more-specific type on two successive calls.

```crystal
if method # first call to a method that can return Int32 or Nil
          # here we know that the first call did not return Nil
  method  # second call can still return Int32 or Nil
end
```

The techniques described above for instance variables will also work for proc and method calls.
