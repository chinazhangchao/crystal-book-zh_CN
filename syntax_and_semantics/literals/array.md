# 数组(Array)

一个 [Array](http://crystal-lang.org/api/Array.html) 是可以用整数做索引的一系列`T`类型的元素。

数组往往以方括号（`[]`）包含住以逗号（`,`）分隔的一系列元素。

```crystal
[1, 2, 3]
```

## 泛型参数

数组的泛型参数 `T` 可以从字面量的类型中推出来，如果数组里的所有元素类型都相同，那它就是那个类型。此外,它就是所有元素类型的或。

```crystal
[1, 2, 3]          # => Array(Int32)
[1, "hello", 'x']  # => Array(Int32 | String | Char)
```

可以用 `of` 加一个类型做显式类型标注，数组， `of` 和类型之间都要用空格隔开。这会覆盖自动推导出的类型。数组一开始只有这个类型的元素，但之后可以添加其他类型的元素。

```crystal
array_of_numbers = [1, 2, 3] of Number  # => Array(Number)
array_of_numbers + [0.5]                # => [1, 2, 3, 0.5]

array_of_int_or_string = [1, 3, 4] of Int32 | String  # => Array(Int32 | String)
array_of_int_or_string + ["foo"]                      # => [1, 2, 3, "foo"]
```

空数组字面量总是需要类型标注：

```crystal
[] of Int32  # => Array(Int32).new
```

## 百分号数组字面量

[字符串数组](./string.html#Percent String Array Literal) 和 [符号数组](./symbol.html#Percent Symbol Array Literal) 可以用百分号字面量创建：

```crystal
%w(one two three)  # => ["one", "two", "three"]
%i(one two three)  # => [:one, :two, :three]
```

## 数组式类型字面量

Crystal对数组和数组型类型有额外的字面量构造方法。他以类型开头，后接大括号(`{}`)，内部填入以逗号(`,`)分割的诸元素。

```crystal
Array{1, 2, 3}
```

这适用于所有有空参构造函数，并且接受 `<<` 的类型。

```crystal
IO::Memory{1, 2, 3}
Set{1, 2, 3}
```

对于一个像 `IO::Memory`这样的非泛型类型，这等价于：

```crystal
array_like = IO::Memory.new
array_like << 1
array_like << 2
array_like << 3
```

对于一个像 `Set`这样的泛型，它的类型参数 `T` 也像数组一样从元素的类型推导而来。上面等价于：

```crystal
array_like = Set(typeof(1, 2, 3)).new
array_like << 1
array_like << 2
array_like << 3
```

类型参数作为类型名的一部分显式指出：

```crystal
Set(Number) {1, 2, 3}
```
