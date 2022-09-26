# 枚举

枚举是一系列与名称相绑定的整数值。例如：

```crystal
enum Color
  Red
  Green
  Blue
end
```

以 `enum`关键字定义枚举。先写关键字，后面接名称。枚举体内包含它们的值，默认从`0`开始，每次加1，不过这个默认值可以覆盖：

```crystal
enum Color
  Red         # 0
  Green       # 1
  Blue   = 5  # 覆盖到 5
  Yellow      # 6 (5 + 1)
end
```

枚举中的每个常量都属于该枚举类型：

```crystal
Color::Red #:: Color
```

用 `value`取得它所代表的值：

```crystal
Color::Green.value #=> 1
```

枚举的底层类型默认是 `Int32` ，但可以修改：

```crystal
enum Color : UInt8
  Red
  Green
  Blue
end

Color::Red.value #:: UInt8
```

只有整数枚举可以设置底层类型。

所有的枚举都继承 [Enum](http://crystal-lang.org/api/Enum.html)。

## 位枚举

枚举可以加 `@[Flags]` 属性。这会改变基础值和增加方式：

```crystal
@[Flags]
enum IOMode
  Read # 1
  Write  # 2
  Async # 4
end
```

`@[Flags]`属性让初始值设为 `1`，后继的每个值都是原值乘以 `2`的积。

这种枚举还会隐式地拥有两个额外变量 `None`和 `All`。 `None` 的值为 `0` ， `All`有所有常量(以位或运算)合并的积。

```crystal
IOMode::None.value #=> 0
IOMode::All.value  #=> 7
```

同时， `Enum`的某些方法会查看 `@[Flags]` 属性，并改变自身的行为。例如：

```crystal
puts(Color::Red)                    # 打印 "Red"
puts(IOMode::Write | IOMode::Async) # 打印 "Write, Async"
```

## 从整数构造枚举

所有的枚举都可以由整数构造：

```crystal
puts Color.new(1) #=> 打印 "Green"
```

不是枚举中元素的的值也可以输入，同样拥有 `Color`类型。但是如果你打印它，就会得到它隐含的值：

```crystal
puts Color.new(10) #=> 打印 "10"
```

这个方法主要用于把C枚举转为Crystal枚举。

## 方法

类似于结构体，你可以给枚举定义方法：

```crystal
enum Color
  Red
  Green
  Blue

  def red?
    self == Color::Red
  end
end

Color::Red.red?  #=> true
Color::Blue.red? #=> false
```

枚举可以有类变量，但不可以有成员变量。

## 用法

枚举是类型安全的 [符号](http://crystal-lang.org/api/Symbol.html)替代品。例如，一个 API的方法可以用枚举类型确定 [类型限制](type_restrictions.html)：

```crystal
def paint(color : Color)
  case color
  when Color::Red
    # ...
  else
    # 罕见，但有可能发生
    raise "unknown color: #{color}"
  end
end

paint Color::Red
```

上式也可以用 Symbol 实现：

```crystal
def paint(color : Symbol)
  case color
  when :red
    # ...
  else
    raise "unknown color: #{color}"
  end
end

paint :red
```

但是，如果程序员错打成 `:reed`，那这个错误会在运行时出现，但尝试输入 `Color::Reed`会导致编译错误。

我们推荐在任何可能的地方都用枚举，只在API内部实现中使用符号，而避免在公共API中使用。不过你可以按你的意愿来决定如何使用它们。
