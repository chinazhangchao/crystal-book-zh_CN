# 类型推导

Crystal的思想是尽量少写类型声明。但是仍有一些声明要写。


```crystal
class Person
  def initialize(@name)
    @age = 0
  end
end
```

我们马上就能看到 `@age` 是整数，但是我们不知道 `@name`是什么类型。编译器会尝试从 `Person` 类的所有使用来推导`@name`的类型。这会导致一些问题：

* 这个类型对读代码的人不友好：他也得检查 `Person`的所有用法来得到推出它。
* 一些编译器优化，比如方法只分析一次，增量编译等，几乎不可能被做到。

随着代码规模增长，这个问题会愈加显著：项目变得更难理解，编译时间原地飞升。

因此， Crystal需要知道成员变量和[类变量](class_variables.html)的类型。清晰的声明会让编译器舒适，也会让阅读者舒适。

如下几种方法可以帮助 Crystal知道这些。

## 类型标注

最简单，也最累的方法是显示标注类型。

```crystal
class Person
  @name : String
  @age : Int32

  def initialize(@name)
    @age = 0
  end
end
```

## 不写类型标注

如果你没有显式地标注类型，编译器就会尝试用它丰富的规则积累推导成员变量或类变量的类型。

对于一个成员/类变量，当从一个规则猜出一种类型时，这个类型会被记录到集合中。等到所有的规律都应用完毕，推导的类型就会是这些类型之 [联合](union_types.html)。另外，如果编译器知道这些变量没有总是得到初始化，它就会把 [Nil](literals/nil.html)类型也加进去。

规则有很多，但只有前三条被经常应用，因此你没必要全记住。如果编译器报错说推不出这个变量的类型，你再添一个声明也不迟。

如下的规则适用于成员变量，但也可以被应用与类变量。

### 1. 字面量赋值

当成员变量被赋以字面量时，字面量的类型被加入候选。所有的 [字面量](literals.html)都关联有一个类型。

下例中， `@name`推导为 `String`，而 `@age`推导为 `Int32`。

```crystal
class Person
  def initialize
    @name = "John Doe"
    @age = 0
  end
end
```

这个规则，和下面的所有规则，也用于`initialize`之外的方法。比如：

```crystal
class SomeObject
  def lucky_number
    @lucky_number = 42
  end
end
```

上例中， `@lucky_number` 会被推导为 `Int32 | Nil`：`Int32` 是因为42被赋给它，而 `Nil`是因为他不总是在所有的初始化方法中都被赋值。

### 2. 赋以调用某个类方法 `new`的结果

当成员变量被赋以一个 `Type.new(...)` 之类的表达式，对应的 `Type`被加入候选。

下例中， `@address` 推导为 `Address`。

```crystal
class Person
  def initialize
    @address = Address.new("somewhere")
  end
end
```

这对泛型也适用。这里 `@values` 推导为 `Array(Int32)`.

```crystal
class Something
  def initialize
    @values = Array(Int32).new
  end
end
```

**注意**：  `new`方法可能在类型中被重定义，如果这样，那么它的类型就是 `new`的返回值类型，如果返回值能凭借接下来的规则推出来。

### 3. 赋以一个在方法中有类型限制的变量

下例中`@name` 推导为 `String` ，因为 `name` 的类型被确定为 `String`，然后他又被赋给了 `@name`。

```crystal
class Person
  def initialize(name : String)
    @name = name
  end
end
```

注意这个参数的名字并不重要。这个例子也顶用：

```crystal
class Person
  def initialize(obj : String)
    @name = obj
  end
end
```

这个行为有语法糖，以下的代码和上面的代码是等效的：

```crystal
class Person
  def initialize(@name : String)
  end
end
```

同时注意，编译器不会检查方法的参数是否被赋予了别的值：

```crystal
class Person
  def initialize(name : String)
    name = 1
    @name = name
  end
end
```

这个例子中，编译器仍然会认为 `@name` 是 `String`，然后报编译错误，说 `Int32` 不能被赋给类型为 `String`的变量。如果 `@name`不应该是`String`，你应当标注出来。

### 4. 赋以返回值有标注的类方法

下例中， `@address` 推导为 `Address`，因为类方法 `Address.unknown`的返回值被标为 `Address`。

```crystal
class Person
  def initialize
    @address = Address.unknown
  end
end

class Address
  def self.unknown : Address
    new("unknown")
  end

  def initialize(@name : String)
  end
end
```

实际上， `self.unknown`用不着标注返回值类型，因为编译器会分析这个类方法。如果它能应用之前的规律(像是 `new`方法或是字面量的规律)，他就能把类方法的返回值推出来。所以，我们可以这么简写：

```crystal
class Person
  def initialize
    @address = Address.unknown
  end
end

class Address
  # 这里不用标注返回值:
  def self.unknown
    new("unknown")
  end

  def initialize(@name : String)
  end
end
```

这个规律非常方便，因为`new`之外的“类似于构造函数”的类方法非常常见。

### 5.赋以方法参数中的默认值

下例中， 因为 `name` 的默认值是字面量，而后他被赋值给 `@name`，所以 `String`也会被加入候选。

```crystal
class Person
  def initialize(name = "John Doe")
    @name = name
  end
end
```

同样的，这也有语法糖：

```crystal
class Person
  def initialize(@name = "John Doe")
  end
end
```

同样地，默认值可以使是 `Type.new(...)` ，或是一个有返回值标注的类方法。

### 6. 赋以 `lib`函数的返回值

因为 [C库函数](c_bindings/fun.html) 肯定有显式的类型，所以编译器可以由此推出被赋值者的类型。

下例中`@age` 推导为 `Int32`。

```crystal
class Person
  def initialize
    @age = LibPerson.compute_default_age
  end
end

lib LibPerson
  fun compute_default_age : Int32
end
```

### 7. 使用 `out` 库表达式

因为 [库函数](c_bindings/fun.html) 的参数肯定有显式的类型，编译器就可以猜出 `out`参数的类型。这应该是一个指针类型，也可能是是指针解引用得到的类型。

下例中`@age` 推导为 `Int32`.

```crystal
class Person
  def initialize
    LibPerson.compute_default_age(out @age)
  end
end

lib LibPerson
  fun compute_default_age(age_ptr : Int32*)
end
```

### 其他规则

编译器会尽它最大的智能来减少你手动标注类型的需要。比如，如果 `if`的结果被拿来赋值，类型就会是 `then`和 `else`分支返回类型的并：

```crystal
class Person
  def initialize
    @age = some_condition ? 1 : 2
  end
end
```

因为上面的`if`  (额，技术上叫三元运算符，不过类似于 `if`)有整数字面量， `@age`就会被成功推导为 `Int32`而不用再标注类型。

另外的例子是 `||` 和 `||=`：

```crystal
class SomeObject
  def lucky_number
    @lucky_number ||= 42
  end
end
```

上例中 `@lucky_number` 推导为 `Int32 | Nil`。这对于惰性初始化的变量非常有用。

也可以用常量赋值，这对于编译器和人类都很友好。

```crystal
class SomeObject
  DEFAULT_LUCKY_NUMBER = 42

  def initialize(@lucky_number = DEFAULT_LUCKY_NUMBER)
  end
end
```

此时应用规则 5 (参数初始值)，然后因为常量是一个整数字面量， `@lucky_number` 就推导为 `Int32`.
