# 实例化，初始化和内存分配

通过调用类的 `new` 方法创建实例：

```
person = Person.new
```

`person` 是 `Person` 的一个实例。

目前 `person` 没啥大用，我们给它加一些概念。一个 `Person` 有名字和年龄。在“一切都是对象”那一节我们说过一个对象属于一个类型、响应一组方法，只能通过调用方法来访问对象的内部状态，因此我们需要 `name` 和 `age` 方法。我们把这些信息存储在实例变量里面，实例变量名以 `@` 开头。我们想让 `Person` 初始化的时候带一个自定义的名字和值为0的年龄。初始化使用一个特殊的方法 `initialize`，这个方法经常被称为“构造函数”：

```crystal
class Person
  def initialize(name : String)
    @name = name
    @age = 0
  end

  def name
    @name
  end

  def age
    @age
  end
end
```

现在可以这样创建 `Person` 对象：

```crystal
john = Person.new "John"
peter = Person.new "Peter"

john.name #=> "John"
john.age #=> 0

peter.name #=> "Peter"
```

（如果你疑惑为啥 `name` 字段指定 `String` 类型，而 `age` 字段没有指定类型，参考[全局类型接口算法](type_inference.html)）

注意，我们使用 `new` 方法创建 `Person` 实例，但是初始化代码是在 `initialize` 方法定义的，不是在 `new` 方法。这是为什么？

原因是当我们定义 `initialize` 方法时，Crystal 为我们定义了一个 `new` 方法，类似这样：

```crystal
class Person
  def self.new(name : String)
    instance = Person.allocate
    instance.initialize(name)
    instance
  end
end
```

首先，注意 `self.new` 这种写法。这是一个[类方法](class_methods.md)，类方法属于 **类** `Person`，不属于类的实例。这就是我们能够使用 `Person.new` 的原因。

其次，`allocate` 是一个底层的类方法，用来创建未初始化的该类对象。本质上它为对象分配所需的内存，然后在该内存上调用 `initialize` 方法，最后返回这个实例。通常你永远不需要调用 `allocate`，因为它是[不安全的](unsafe.html)。这就是 `new` 和 `initialize` 如何关联的。
