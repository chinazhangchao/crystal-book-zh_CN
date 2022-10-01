# 类型语法

当：

* 指定 [类型限制](type_restrictions.html)
* 指定 [类型参数](generics.html)
* [声明变量](declare_var.html)
* 声明 [别名](alias.html)
* 声明 [C typedef](c_bindings/type.html)
* [is_a?](is_a.html) 伪方法的参数
* [as](as.html) 表达式的参数
* [sizeof](sizeof.html)表达式的参数
* [instance_sizeof](instance_sizeof.html) 表达式的参数
* 方法的 [返回类型](return_types.html)

对于常见的类型，我们提供了一些便捷语法。这在写 [C 绑定](c_bindings/index.html)时尤其有用，但是可以用于任何的这些地方。

## Paths and generics

常规类型和泛型都可以用：

```crystal
Int32
My::Nested::Type
Array(String)
```

## 联合类型

```crystal
alias Int32OrString = Int32 | String
```

类型之间的管道符 (`|`) 创建了一个联合类型。 `Int32 | String` 读作 "Int32 或 String"。通常代码中， `Int32 | String` 意思是以`String`为参数，调用 `Int32` 的`|` 方法。

## 可空类型

```crystal
alias Int32OrNil = Int32?
```

等同于:

```crystal
alias Int32OrNil = Int32 | ::Nil
```

通常代码中， `Int32?` 是语法错误。

## 指针

```crystal
alias Int32Ptr = Int32*
```

等同于:

```crystal
alias Int32Ptr = Pointer(Int32)
```

通常代码中，`Int32*` 意味着调用 `Int32`的 `*`方法。

## 静态数组

```crystal
alias Int32_8 = Int32[8]
```

等同于：

```crystal
alias Int32_8 = StaticArray(Int32, 8)
```

通常代码中， `Int32[8]` 意味着以`8`为参数调用`Int32` 的 `[]`方法。

## 元组

```crystal
alias Int32StringTuple = {Int32, String}
```

is the same as:

```crystal
alias Int32StringTuple = Tuple(Int32, String)
```

通常代码中， `{Int32, String}` 是一个元组**实例**，包含 `Int32` 和 `String` 两个元素。这不同于上面的元组 **类型**。

## 命名元组

```crystal
alias Int32StringNamedTuple = {x: Int32, y: String}
```

等同于

```crystal
alias Int32StringNamedTuple = NamedTuple(x: Int32, y: String)
```

通常代码中， `{x: Int32, y: String}` 是命名元组**实例**，把 `x`对应到`Int32`  , `y`对应到 `String` 。 这不同于上面的命名元组 **类型**。

## 闭包

```crystal
alias Int32ToString = Int32 -> String
```

等同于

```crystal
alias Int32ToString = Proc(Int32, String)
```

声明一个无参数的闭包：

```crystal
alias ProcThatReturnsInt32 = -> Int32
```

指定多个闭包参数：

```crystal
alias Int32AndCharToString = Int32, Char -> String
```

对于嵌套的闭包，可以用括号来分别层次，这不限于括号，适用于任何类型：

```crystal
alias ComplexProc = (Int32 -> Int32) -> String
```

通常代码中， `Int32 -> String`是语法错误。

## self

`self` 可以用作类型，表示 `self`的类型。详见 [类型限制](type_restrictions.html)一章。

## class

`class` 用于指定*类* 类型，而不是实例类型。

例如：

```crystal
def foo(x : Int32)
  "instance"
end

def foo(x : Int32.class)
  "class"
end

foo 1     # "instance"
foo Int32 # "class"
```

`class` 也用于创建包含类类型的数组：

```crystal
class Parent
end

class Child1 < Parent
end

class Child2 < Parent
end

ary = [] of Parent.class
ary << Child1
ary << Child2
```

## 下划线

下划线用于类型限制时，表示“任何类型：

```crystal
# 等同于不写类型限制，用处不大
def foo(x : _)
end

# 有点用处: 指定输入任意两个类型，返回 Int32：
def foo(x : _, _ -> Int32)
end
```

## typeof

`typeof` 可以用于类型语法，表示其传入所有变量的类型之并：

```crystal
typeof(1 + 2) # => Int32
typeof(1, "a") # => (Int32 | String)
```
