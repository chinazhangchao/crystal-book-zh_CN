# 模块

模块有两种功能：

* 作为命名空间容纳类型，方法和常量的定义
* 作为部分类型，用于混入(mix in)其他类型。

作为命名空间的例子：

```crystal
module Curses
  class Window
  end
end

Curses::Window.new
```

库作者应当把它们的定义写到模块中，以避免名称冲突。标准库则因为其中的类型十分常用，就直接放到顶层命名空间，以避免写长名称。

用 `include`或 `extend`将模块作为部分类型使用。

`include` 让类型的实例拥有模块中定义的方法：

```crystal
module ItemsSize
  def size
    items.size
  end
end

class Items
  include ItemsSize

  def items
    [1, 2, 3]
  end
end

items = Items.new
items.size #=> 3
```

上例中，效果好像我们把size里面的`size`方法从模块中粘到 `Items` 类里面一样。这个实际上是通过让每个类型有多个祖先来完成的。这些祖先以超类开头，所有的模块都添加在排超类的**后面**。当类型中找不到某个方法时它就会从祖先列表中依次查找。当调用`super`时，他会用祖先列表中的第一个。

模块也可以包含其他模块，因此当模块中没有某个方法时，他会从包含的模块中找这个方法。

`extend` 让一个类本身拥有模块中的方法：

```crystal
module SomeSize
  def size
    3
  end
end

class Items
  extend SomeSize
end

Items.size #=> 3
```

`include`和 `extend` 都让模块中的常量在被包含、补全的类型中可见。

他们也能用于顶层命名空间以避免重复表示命名空间(同时增加命名冲突的风险)：

```crystal
module SomeModule
  class SomeType
  end

  def some_method
    1
  end
end

include SomeModule

SomeType.new # OK, 等同于 SomeModule::SomeType
some_method  # OK, 1
```

## extend self

模块的常见用法是 `extend self`:

```crystal
module Base64
  extend self

  def encode64(string)
    # ...
  end

  def decode64(string)
    # ...
  end
end
```

这种情况模块自己就像一个对象，可以用`.`而不是`::`调取其中的方法：

```crystal
Base64.encode64 "hello" #=> "aGVsbG8="
```

同时它也可以在程序中被包含，以省略命名空间：

```crystal
include Base64

encode64 "hello" #=> "aGVsbG8="
```

为了充分利用这个功能，方法名应当能显示所在的模块，否则命名冲突的概率就会很高。

模块不能被实例化：

```crystal
module Moo
end

Moo.new # 未定义方法 'new' 于 Moo:Class 中
```
