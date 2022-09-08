# 类变量

类变量由类所有，而不由对象所有。它们以双"at"(`@@`)开头。例如：

```crystal
class Counter
  @@instances = 0

  def initialize
    @@instances += 1
  end

  def self.instances
    @@instances
  end
end

Counter.instances #=> 0
Counter.new
Counter.new
Counter.new
Counter.instances #=> 3
```

类变量可以由类方法和实例方法读写。

它们的类型也可以被 [全局类型推导规则](type_inference.html)推导。

类变量也可以继承，并遵循如下规则：它们的名称是一样的，但是每个类都分别含有一个运行时的值。例如：

```crystal
class Parent
  @@numbers = [] of Int32

  def self.numbers
    @@numbers
  end
end

class Child < Parent
end

Parent.numbers # => []
Child.numbers # => []

Parent.numbers << 1
Parent.numbers # => [1]
Child.numbers # => []
```

类变量也可以从属于模块和结构体，它们也以相同的方式被包含/派生的类型继承。
