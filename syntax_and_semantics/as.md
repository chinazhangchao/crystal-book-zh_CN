# as

`as` 伪方法可以限制表达式的类型。比如：

```crystal
if some_condition
  a = 1
else
  a = "hello"
end

# a : Int32 | String
```

在上述代码中，`a` 是或类型 `Int32 | String`。如果在 `if` 表达式之后还要把 `a` 用作 `Int32` ，我们可以强制编译器把它视为 `Int32`：

```crystal
a_as_int = a.as(Int32)
a_as_int.abs          # 行，编译器知道 a_as_int 是 Int32
```

`as`伪方法会在运行时检查： 如果`a` 不是 `Int32`，就抛出一个 [exception](exception_handling.html)。 raised.

表达式的形参是一个 [类型](type_grammar.html).

如果一个类型不可能攒成另一个类型，那么会产生编译错误：

```crystal
1.as(String) # 编译错误
```

**注意：** 你不能用 `as` 把一个类型转成不相关的另一个： `as` 不是其他语言中的 `cast`。整数，浮点数，字符都为这些转换提供了方法。不过，可以对指针进行转换，方法如下：

## 指针类型互转

`as` 伪方法也用于转换不同类型的指针：

```crystal
ptr = Pointer(Int32).malloc(1)
ptr.as(Int8*)                    #:: Pointer(Int8)
```

这种情况不会要求运行时检查：指针本来就不安全，并且这种转换往往用于绑定C函数或其他更底层的代码。

## 指针与其他类型互转

指针也可以与成其他类型互相转换：

```crystal
array = [1, 2, 3]

# object_id 返回这个对象在内存中的地址
# 所以我们用这个地址创建指针
ptr = Pointer(Void).new(array.object_id)

# 我们把指针转换成别的类型，但是它们指向的地址一样，因此是相等的。
array2 = ptr.as(Array(Int32))
array2.same?(array) #=> true
```

由于涉及指针，这里没有进行运行时检查。这种用法比上一个更少见，但是允许Crystal自己去实现一些核心类型(比如String)，由此也可以把引用类型当做void*传给C函数。

## 转换到更弱的类型

The `as` 伪方法可以把表达式转给*更弱的*类型。比如：

```crystal
a = 1
b = a.as(Int32 | Float64)
b #:: Int32 | Float64
```

也许不那么有用。但有时也能派上用场，比如映射数组中的元素：

```crystal
ary = [1, 2, 3]

# 通过数组 1, 2, 3 构建 Int32 | Float64 数组
ary2 = ary.map { |x| x.as(Int32 | Float64) }

ary2 #:: Array(Int32 | Float64)
ary2 << 1.5 # OK
```

`Array#map` 方法认为block的返回类型就是数组的类型。如果没有 `as` 伪方法，推到出来的类型就是 `Int32`我们就不能把 `Float64`加进去。

## 如果编译器推导不出块的类型

有时编译器推导不出块的类型，比如递归调用互相依赖起来的时候。此时你可以用 `as`提醒编译器：

```crystal
some_call { |v| v.method.as(ExpectedType) }
```
