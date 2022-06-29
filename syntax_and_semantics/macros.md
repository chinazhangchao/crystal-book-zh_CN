# 宏
宏在编译时接受AST节点,产生代码,并复制进原程序。例如：
```crystal
macro define_method(name, content)
  def {{name}}
    {{content}}
  end
end

# 产生:
#
#     def foo
#       1
#     end
define_method foo, 1

foo #=> 1
```

宏的定义体就像普通的Crystal代码一样，除了用于操作AST的特殊语法。产出的代码必须是有效的Crystal代码，即你不能产出`def`而不配对`end`，或是一个单独的 `when` 而没有 `case`， 因为这不是完整的语法结构. 详见[Pitfalls](#pitfalls).

## 定义域

顶层定义的宏做任何地方都可见，如果顶层宏标注为  `private` 那他只在文档内可见

它们也可以定义在类和模块里，只在这些域中可见。祖先链中的宏也会被查找到（超类和包含的模块）。

比如，块，被一个对象来调用  `with ... yield` 时，也可以找到对象的祖先链中的宏：

```crystal
class Foo
  macro emphasize(value)
    "***#{ {{value}} }***"
  end

  def yield_with_self
    with self yield
  end
end

Foo.new.yield_with_self { emphasize(10) } #=> "***10***"
```

类和模块里面的宏也可以在外面调用：

```crystal
class Foo
  macro emphasize(value)
    "***#{ {{value}} }***"
  end
end

Foo.emphasize(10) # => "***10***"
```

## 插值(Interpolation)

你可以用 `{{...}}`去粘贴、插入一个 AST 节点, 如上所示.

注意：粘贴的方法遵循“如同(as-is)”规则.如果上一个例子中我们放入一个符号，产出的代码将出错：

```crystal
# This generates:
#
#     def :foo
#       1
#     end
define_method :foo, 1
```

注意 `:foo` 是插值的结果，因为传到宏里面的就是它。这种情况你可以调用 `ASTNode#id`,得到它对应的字符(identifier)。

## 宏调用

你可以编译时调用AST节点的方法的一些**固定的子集**。这些方法记录在一个假想的 [Crystal::Macros](http://crystal-lang.org/api/Crystal/Macros.html) 模块。

例如在上面的问题里面调用 `ASTNode#id`：

```crystal
macro define_method(name, content)
  def {{name.id}}
    {{content}}
  end
end

# 正确的输出是：
#
#     def foo
#       1
#     end
define_method :foo, 1
```

## 模块和类

宏也可以用于产生模块，类和结构体：

```crystal
macro define_class(module_name, class_name, method, content)
  module {{module_name}}
    class {{class_name}}
      def initialize(@name : String)
      end

      def {{method}}
        {{content}} + @name
      end
    end
  end
end

# 输出为：
#     module Foo
#       class Bar
#         def initialize(@name : String)
#         end
#
#         def say
#           "hi " + @name
#         end
#       end
#     end
define_class Foo, Bar, say, "hi "

p Foo::Bar.new("John").say # => "hi John"
```

## 条件

你可以用 `{% if condition %}` ... `{% end %}` 为产生代码设定条件：

```crystal
macro define_method(name, content)
  def {{name}}
    {% if content == 1 %}
      "one"
    {% elsif content == 2 %}
      "two"
    {% else %}
      {{content}}
    {% end %}
  end
end

define_method foo, 1
define_method bar, 2
define_method baz, 3

foo #=> one
bar #=> two
baz #=> 3
```

类似于常规代码, `Nop`, `NilLiteral` 和一个假 `BoolLiteral` 被认为 *是假的*，其他所有东西都是真的。

宏条件在宏定义外面也可以用：

```crystal
{% if env("TEST") %}
  puts "我们在测试模式"
{% end %}
```

## 迭代

你可以迭代有限次：

```crystal
macro define_constants(count)
  {% for i in (1..count) %}
    PI_{{i.id}} = Math::PI * {{i}}
  {% end %}
end

define_constants(3)

PI_1 #=> 3.14159...
PI_2 #=> 6.28318...
PI_3 #=> 9.42477... 
```

迭代一个 `ArrayLiteral`:

```crystal
macro define_dummy_methods(names)
  {% for name, index in names %}
    def {{name.id}}
      {{index}}
    end
  {% end %}
end

define_dummy_methods [foo, bar, baz]

foo #=> 0
bar #=> 1
baz #=> 2
```

上例中 `index`变量是可选的.

迭代一个 `HashLiteral`:

```crystal
macro define_dummy_methods(hash)
  {% for key, value in hash %}
    def {{key.id}}
      {{value}}
    end
  {% end %}
end
define_dummy_methods({foo: 10, bar: 20})
foo #=> 10
bar #=> 20
```

迭代在宏定义外面也可以用：

```crystal
{% for name, index in ["foo", "bar", "baz"] %}
  def {{name.id}}
    {{index}}
  end
{% end %}

foo #=> 0
bar #=> 1
baz #=> 2
```

## 可变数量参数和 嵌入(splatting)

宏可以接受可变数量参数：

```crystal
macro define_dummy_methods(*names)
  {% for name, index in names %}
    def {{name.id}}
      {{index}}
    end
  {% end %}
end

define_dummy_methods foo, bar, baz

foo #=> 0
bar #=> 1
baz #=> 2
```

这些参数被装进一个 `ArrayLiteral` 传给宏。

另外 `*`前缀可以把 `ArrayLiteral`里面的项用逗号相连，然后放出来:

```crystal
macro println(*values)
  print {{*values}}, '\n'
end

println 1, 2, 3 # 输出 123\n
```

## 类型信息

调用宏时你可以用`@type`这个特殊的实例变量访问这个区域的类型、域。它自己的类型是 `TypeNode`用于在编译时给你类型信息。

注意 `@type` 总是 *实例* 类型, 即使宏是在类方法中调用。

例如：

```crystal
macro add_describe_methods
  def describe
    "类型是: " + {{ @type.stringify }}
  end
  
  def self.describe
    "类型是: " + {{ @type.stringify }}
  end
end

class Foo
  add_describe_methods
end

Foo.new.describe #=> "类型是 Foo"
Foo.describe #=> "类型是 Foo"
```

## 常量

宏可以访问常量，例如：

```crystal
VALUES = [1, 2, 3]

{% for value in VALUES %}
  puts {{value}}
{% end %}
```

如果常量是类型，你会得到 `TypeNode`.

## 嵌套宏

你甚至可以定义一个宏来产生更多的宏定义。你必须把里面的宏行与行之间用反斜杠"\\"连接起来，以防止它被外面的宏求值

```crystal
macro define_macros(*names)
  {% for name in names %}
    macro greeting_for_{{name.id}}(greeting)
      \{% if greeting == "hola" %}
        "¡hola {{name.id}}!"
      \{% else %}
        "\{{greeting.id}} {{name.id}}"
      \{% end %}
    end
  {% end %}
end

# 输出为：
#
#     macro greeting_for_alice
#       {% if greeting == "hola" %}
#         "¡hola alice!"
#       {% else %}
#         "{{greeting.id}} alice"
#       {% end %}
#     end
#     macro greeting_for_bob
#       {% if greeting == "hola" %}
#         "¡hola bob!"
#       {% else %}
#         "{{greeting.id}} bob"
#       {% end %}
#     end
define_macros alice, bob

greeting_for_alice "hello"  #=> "hello alice"
greeting_for_bob "hallo"    #=> "hallo bob"
greeting_for_alice "hej"    #=> "hej alice"
greeting_for_bob "hola"     #=> "¡hola bob!"
```

## 切记

写宏时切记宏产生的代码必须，在插入原位置之前，自己就是合法的Crystal代码。例如，宏里面如果没有`case`作为表达式开头,那就不能有孤立的`when`。

这是个不合法的宏：

```crystal
case 42
{% for klass in [Int32, String] %}
  when {{klass.id}}
    p "is {{klass}}"
{% end %}
end
```

注意 `case`不在宏里面。这个宏产生孤立的 `when` 表达式，就不是合法的。你必须用`begin`和`end`来把 `case`包进去:

```crystal
{% begin %}
  case 42
  {% for klass in [Int32, String] %}
    when {{klass.id}}
      p "is {{klass}}"
  {% end %}
  end
{% end %}
```
