# 可见性

方法默认是公开的：编译期总会让你调用它即使你没加 `public`关键字。

方法可以被标记为 `private`或 `protected`。

## 私有方法

`private` 方法调用时不能有接受者。意思是没有点号前面的东西：

```crystal
class Person
  private def say(message)
    puts message
  end

  def say_hello
    say "hello" # OK, 没有接受者
    self.say "hello" # 错误, self 是接受者

    other = Person.new "Other"
    other.say "hello" # 错误, 是接受者
  end
end
```

注意 `private` 方法对子类型可见：

```crystal
class Employee < Person
  def say_bye
    say "bye" # OK
  end
end
```

## 私有类型

私有类型只能在它们被定义的命名空间中被提及，并且永远不能被完全注明(即从顶层命名空间找到)。

```crystal
class Foo
  private class Bar
  end

  Bar      # OK
  Foo::Bar # Error
end

Foo::Bar # Error
```

`private` 可以应用于 `class`，`module`，`lib`，`enum`，`alias`和常量：

```crystal
class Foo
  private ONE = 1

  ONE # => 1
end

Foo::ONE # Error
```

## 保护方法

`protected`只能被这样调用：

1. 当前类型的实例
2. 当前类型所在命名空间中的实例

```crystal
### 例 1

class Person
  protected def say(message)
    puts message
  end

  def say_hello
    say "hello" # OK, self 是 Person, 尽管没有写出
    self.say "hello" # OK, self 是 Person

    other = Person.new "Other"
    other.say "hello" # OK, other 是 Person
  end
end

class Animal
  def make_a_person_talk
    person = Person.new
    person.say "hello" # 错误, person 是 Person
                       # 但当前类型是 Animal
  end
end

one_more = Person.new "One more"
one_more.say "hello" # 错误, one_more 是 Person
                     # 但当前类型是 Program 整个程序

### 例 2

module Namespace
  class Foo
    protected def foo
      puts "Hello"
    end
  end

  class Bar
    def bar
      # 行, 因为 Foo 和 Bar 都处于 Namespace 中
      Foo.new.foo
    end
  end
end

Namespace::Bar.new.bar
```

`protected` 类方法可以从实例方法调用，或是其他周转的方法：

```crystal
class Person
  protected def self.say(message)
    puts message
  end

  def say_hello
    Person.say "hello" # OK
  end
end
```

## 私有的顶层方法

`private`顶层方法只在当前文档中可见。

```crystal
# 在文件 one.cr 中
private def greet
  puts "Hello"
end

greet #=> "Hello"

# 在文件 two.cr 中
require "./one"

greet # 未定义的局部变量或方法 'greet'
```

这可以帮助你把辅助方法局限在这个文档中，如果他们不应暴露出去。

## 私有的顶层类型

`private`顶层类型只在当前文档中可见。

```crystal
# 在文件 one.cr 中
private class Greeter
  def self.greet
    "Hello"
  end
end

Greeter.greet #=> "Hello"

# 在文件 two.cr 中
require "./one"

Greeter.greet # 未定义的常量 'Greeter'
```
