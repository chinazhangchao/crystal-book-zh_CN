# 方法和实例变量

通过一种简短的语法我们可以简化构造函数，把方法参数名改为实例变量名：

```crystal
class Person
  def initialize(@name : String)
    @age = 0
  end
  
  def age
    @age
  end
end
```

目前，我们所能做的只有用一个名字创建 `Person` 对象。它的年龄永远是0。让我们增加一个让 person 变老的方法：

```crystal
class Person
  def initialize(@name : String)
    @age = 0
  end
  
  def age
    @age
  end
  
  def become_older
    @age += 1
  end
end

john = Person.new "John"
peter = Person.new "Peter"

john.age #=> 0

john.become_older
john.age #=> 1

peter.age #=> 0
```

方法名以小写字母开头，按照惯例，只能使用小写字母、下划线和数字。

## 读写宏

Crystal [标准库](https://crystal-lang.org/api)提供了宏，可以简化读写方法的定义：

```crystal
class Person
  property age
  getter name : String

  def initialize(@name)
    @age = 0
  end
end

john = Person.new "John"
john.age = 32
john.age #=> 32
```

想了解更多关于读写宏的信息，参考标准库文档 [Object#getter](https://crystal-lang.org/api/latest/Object.html#getter%28%2Anames%29-macro)， [Object#setter](https://crystal-lang.org/api/latest/Object.html#setter%28%2Anames%29-macro)， and [Object#property](https://crystal-lang.org/api/latest/Object.html#property%28%2Anames%29-macro)。

另外，我们可以在原始的 `Person` 定义中定义 `become_older` 方法，也可以分开定义，Crystal 会合并所有的定义到一个类中。下面是合法的代码：

```crystal
class Person
  def initialize(@name : String)
    @age = 0
  end
end

class Person
  def become_older
    @age += 1
  end
end
```

## 重定义方法和previous_def

如果重定义一个方法，最后一次定义的优先级最高。

```crystal
class Person
  def become_older
    @age += 1
  end
end

class Person
  def become_older
    @age += 2
  end
end

person = Person.new "John"
person.become_older
person.age #=> 2
```

可用通过 `previous_def` 调用之前定义的方法（译注：多次定义可以递归）：

```crystal
class Person
  def become_older
    @age += 1
  end
end

class Person
  def become_older
    previous_def
    @age += 2
  end
end

person = Person.new "John"
person.become_older
person.age #=> 3
```

不带括号和参数的情况下，`previous_def` 将使用当前方法调用时的参数。否则使用传入的参数。

## “一网打尽”初始化

实例变量也可以在 `initialize` 方法外面初始化：

```crystal
class Person
  @age = 0

  def initialize(@name : String)
  end
end
```

这会在所有构造函数中把 `@age` 初始化为0。这对减少重复代码非常有用，也避免重新打开类增加实例变量时产生 `Nil` 类型。
