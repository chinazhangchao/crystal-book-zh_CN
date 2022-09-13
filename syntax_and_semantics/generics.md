# 泛型

泛型使你可以基于其他类型制定一个参数化类型。考虑这个 Box 类型：

```crystal
class MyBox(T)
  def initialize(@value : T)
  end

  def value
    @value
  end
end

int_box = MyBox(Int32).new(1)
int_box.value # => 1 (Int32)

string_box = MyBox(String).new("hello")
string_box.value # => "hello" (String)

another_box = MyBox(String).new(1) # 错误, Int32 不匹配 String
```

泛型常用于制作集合类型。`Array`，`Hash`，`Set` 都是泛型， `Pointer`也是。

类型参数可以不止一个：

```crystal
class MyDictionary(K, V)
end
```

任何类型都能做类型参数：

```crystal
class MyDictionary(KeyType, ValueType)
end
```

## 类型变量推导

当不指定类型参数时，编译器会尝试由构造函数中的变量类型推导泛型类型参数。例如：

```crystal
MyBox.new(1)       # : MyBox(Int32)
MyBox.new("hello") # : MyBox(String)
```

上例中我们不必指定 `MyBox`缝纫类型参数，编译器会以如下的方式确定它：

* `MyBox.new(value)` 委派给 `initialize(@value : T)`
* `T` 还没有绑定到一个类型，所以编译器把它绑定到给出参数变量的类型

这样设计和构造泛型就会变得更方便。

## 泛型结构体和模块

结构体和模块也可以是泛型。当模块是泛型时你可以这样包括它：

```crystal
module Moo(T)
  def t
    T
  end
end

class Foo(U)
  include Moo(U)

  def initialize(@value : U)
  end
end

foo = Foo.new(1)
foo.t # Int32
```

注意，上例中因为`Foo.new(1)` 表明了 `U` 是 `Int32`，所以 `T` 是 `Int32` ，继而在包含的模块中 `T` 也是 `Int32`。

## 泛型类型继承

泛型类和结构体也可以继承。当继承时你可以指定一个泛型实例，也可以把类型变量传递出去：

```crystal
class Parent(T)
end

class Int32Child < Parent(Int32)
end

class GenericChild(T) < Parent(T)
end
```
