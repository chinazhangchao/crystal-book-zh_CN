# case

`case` 是类似于模式匹配的控制表达式，他可以等效于一系列 if-else-if 表达式，但也有更强大的功能。

在最基本的结构中，它可以比对一个值与其他值：

```crystal
case exp
when value1, value2
  do_something
when value3
  do_something_else
else
  do_another_thing
end

# 上式等同于:
tmp = exp
if value1 === tmp || value2 === tmp
  do_something
elsif value3 === tmp
  do_something_else
else
  do_another_thing
end
```

表达式与 `case`的值是否等价，这由 *等价判断运算符* `===` 决定。这是 [`Object`](https://crystal-lang.org/api/Object.html#%3D%3D%3D%28other%29-instance-method) 中定义的一个方法，也可以由其他类重载以提供特定的的匹配语义。比如， [`Class`](https://crystal-lang.org/api/Class.html#%3D%3D%3D%28other%29-instance-method) 进行匹配的方法是看该对象是否是这个类的实例， [`Regex`](https://crystal-lang.org/api/Regex.html#%3D%3D%3D%28other%3AString%29-instance-method) 则看这个值(往往是字符串)是否和正则表达式匹配，而 [`Range`](https://crystal-lang.org/api/Range.html#%3D%3D%3D%28value%29-instance-method) 则检查这个值是否处于范围之中。

如果 `when`对应的表达式是一个类型，就用 `is_a?` 来判断。另外，在不同的分支中，表达式的类型也会有更准确的限制：

```crystal
case var
when String
  # var : String
  do_something
when Int32
  # var : Int32
  do_something_else
else
  # 这里 var 既不是 String 也不是 Int32
  do_another_thing
end

# 上式等同于:
if var.is_a?(String)
  do_something
elsif var.is_a?(Int32)
  do_something_else
else
  do_another_thing
end
```

你可以通过隐式对象语法在`when`中调用 `case`表达式的方法：

```crystal
case num
when .even?
  do_something
when .odd?
  do_something_else
end

# 上式等同于:
tmp = num
if tmp.even?
  do_something
elsif tmp.odd?
  do_something_else
end
```

你可以在  `when` 表达式后面接`then` 来避免另起一行。

```crystal
case exp
when value1, value2 then do_something
when value3         then do_something_else
else                     do_another_thing
end
```

最后，你可以省略 `case`的值：

```crystal
case
when cond1, cond2
  do_something
when cond3
  do_something_else
end

# 上式等同于:
if cond1 || cond2
  do_something
elsif cond3
  do_something_else
end
```

这有时会增强代码可读性。

## 元组字面量

如果 case 的值和when的条件是元组字面量，case的行为会有一些特殊。

### Tuple size must match

```crystal
case {value1, value2}
when {0, 0} # OK, 2 个元素
  # ...
when {1, 2, 3} # 编译错误，这个分支绝不会被匹配到
  # ...
end
```

### 忽略变量

```crystal
case {value1, value2}
when {0, _}
  # 当 0 === value1, 时匹配，不对 value2 做检查
when {_, 0}
  # 当 0 === value2, 时匹配，不对 value1 做检查
end
```

### 元组中的隐式对象调用

```crystal
case {value1, value2}
when {.even?, .odd?}
  # 当 value1.even? && value2.odd? 时匹配
end
```

### 对类型匹配就是使用 is_a? 进行检查

```crystal
case {value1, value2}
when {String, Int32}
  # 当 value1.is_a?(String) && value2.is_a?(Int32) 时匹配
  # 此时编译器知道 value1 是 String, value2 是Int32
end
```
