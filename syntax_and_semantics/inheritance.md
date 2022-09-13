# 继承

每个类，除了 `Object`这个继承体系的根基，都继承于另一个类(它的超类)。如果你不指定它，那么它对类默认是`Reference`，对结构体默认是`Struct`。

一个类继承超类的所有成员变量，示例和类方法。包括它的构造函数 (`new`和 `initialize`)。

```crystal
class Person
  def initialize(@name : String)
  end

  def greet
    puts "Hi, I'm #{@name}"
  end
end

class Employee < Person
end

employee = Employee.new "John"
employee.greet # "Hi, I'm John"
```

如果一个类定义了 `new`或 `initialize`，那么它的超类的构造函数就不会被继承：

```crystal
class Person
  def initialize(@name : String)
  end
end

class Employee < Person
  def initialize(@name : String, @company_name : String)
  end
end

Employee.new "John", "Acme" # OK
Employee.new "Peter" # 错误: 参数个数不匹配
                     # 于 'Employee:Class#new' (所需 2, 已给 1)
```

你可以在派生类中覆盖方法：

```crystal
class Person
  def greet(msg)
    puts "Hi, #{msg}"
  end
end

class Employee < Person
  def greet(msg)
    puts "Hello, #{msg}"
  end
end

p = Person.new
p.greet "everyone" # "Hi, everyone"

e = Employee.new
e.greet "everyone" # "Hello, everyone"
```

你可以对方法指定类型限制将其特化，以避免重载超类的方法：

```crystal
class Person
  def greet(msg)
    puts "Hi, #{msg}"
  end
end

class Employee < Person
  def greet(msg : Int32)
    puts "Hi, this is a number: #{msg}"
  end
end

e = Employee.new
e.greet "everyone" # "Hi, everyone"

e.greet 1 # "Hi, this is a number: 1"
```

## 超类

你可以用 `super` 指定调用超类的方法：

```crystal
class Person
  def greet(msg)
    puts "Hello, #{msg}"
  end
end

class Employee < Person
  def greet(msg)
    super # 等同于: super(msg)
    super("another message")
  end
end
```

如果没有参数或括号， `super`接受该方法相同的参数。如果有，它就会接受你指定给它的。
