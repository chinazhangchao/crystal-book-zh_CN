# 类型限制

类型限制是施加于方法参数上的类型声明，用于限制方法接受的参数。

```crystal
def add(x : Number, y : Number)
  x + y
end

# Ok
add 1, 2 # Ok

# 错误: 'add' 没有重载匹配类型 Bool, Bool
add true, false
```

注意如果我们定义 `add`时没有增加类型约束，最终还是会得到编译错误：

```crystal
def add(x, y)
  x + y
end

add true, false
```

他会产生如下的错误：

```
错误:  foo.cr:6: 实例化 'add(Bool, Bool)' 时:

add true, false
^~~

foo.cr:2: Bool 没有定义方法 '+' 

  x + y
    ^
```

这是因为你调用 `add`时，他会以参数的类型实例化：以每一种类型组合调用方法都会得到不同的实例化方法。

唯一的区别就是第一个错误信息更加清晰。但是两种定义都是安全的，因为错误的调用都会产生编译错误。所以总的来说，最好不要指定类型限制，只在定义不同的方法重载时使用它。这可以产生更加泛化，更加可用的代码。例如，如果我们对`Number`意外的类型定义 `+` 方法，我们就可以使用不带类型限制的 `add`方法，但是我们就不能使用有限制的那个 `add`。

```crystal
# 一个有 '+' 方法却不是 Number 的类:
class Six
  def +(other)
    6 + other
  end
end

# 不带类型限制的add方法:
def add(x, y)
  x + y
end

# OK
add Six.new, 10

# 带类型限制的add方法:
def restricted_add(x : Number, y : Number)
  x + y
end

# 错误: 'restricted_add'没有重载以匹配类型 Six, Int32
restricted_add Six.new, 10
```

对于类型限制的写法，参考[类型语法](type_grammar.html)。

注意类型限制不适用于方法内部的变量：

```crystal
def handle_path(path : String)
  path = Path.new(path) # *path* 现在是 Path
  # 对 *path* 做点什么
end
```

## self 限制

`self` 特殊的类型限制是：

```crystal
class Person
  def ==(other : self)
    other.name == name
  end

  def ==(other)
    false
  end
end

john = Person.new "John"
another_john = Person.new "John"
peter = Person.new "Peter"

john == another_john #=> true
john == peter #=> false (names differ)
john == 1 #=> false (because 1 is not a Person)
```

上例中 `self` 等同于写 `Person`。但是一般来说， `self` 等同于写最终要拥有这个方法的类型。这对于模块更有用；

另外来说，既然 `Person`继承了 `Reference`。第二个 `==` 的定义就不必要了，因为它已经定义在 `Reference`里。

注意 `self`总是代表实例类型，即使是在类方法中：

```crystal
class Person
  def self.compare(p1 : self, p2 : self)
    p1.name == p2.name
  end
end

john = Person.new "John"
peter = Person.new "Peter"

Person.compare(john, peter) # OK
```

你可以用 `self.class`指代 Person类型。下一章将介绍类型限制中的 `.class`后缀。

## 类作为类型限制

举个例子， `Int32` 作为类型限制使方法只能接受 `Int32`的实例：

```crystal
def foo(x : Int32)
end

foo 1       # OK
foo "hello" # Error
```

如果你想要方法只接受类型 Int32 (而不是它的示例)你应该用 `.class`：

```crystal
def foo(x : Int32.class)
end

foo Int32  # OK
foo String # Error
```

这对于指定类型——而不是实例——进行方法重载时很有用：

```crystal
def foo(x : Int32.class)
  puts "Got Int32"
end

def foo(x : String.class)
  puts "Got String"
end

foo Int32  # prints "Got Int32"
foo String # prints "Got String"
```

## 元组展开中的类型限制

你可以指定不定数目参数的类型限制：

```crystal
def foo(*args : Int32)
end

def foo(*args : String)
end

foo 1, 2, 3       # OK, 调用第一个重载
foo "a", "b", "c" # OK, 调用第二个重载
foo 1, 2, "hello" # Error
foo()             # Error
```

当指定类型时，元组中所有的参数都要匹配这个类型。另外，空元组不会匹配上面任何一种类型。如果你要指定空元组的情况，就再加一个重载：

```crystal
def foo
  # This is the empty-tuple case
end
```

一种匹配一个或多个任意类型参数的方法是用 `Object` 做限制：

```crystal
def foo(*args : Object)
end

foo() # Error
foo(1) # OK
foo(1, "x") # OK
```

## 类型参数

你可以用`forall`关键字让类型限制拥有类型参数 :

```crystal
def foo(x : T) forall T
  T
end

foo(1)       #=> Int32
foo("hello") #=> String
```

此时， `T` 成为了用于实例化方法的类型。

一个类型变量可以用于抽取出泛型类型限制中的参数类型：

```crystal
def foo(x : Array(T)) forall T
  T
end

foo([1, 2])   #=> Int32
foo([1, "a"]) #=> (Int32 | String)
```

为了创建接受类型名(而不是实例)的方法，就给这个类型变量加 `.class`：

```crystal
def foo(x : T.class) forall T
  Array(T)
end

foo(Int32)  #=> Array(Int32)
foo(String) #=> Array(String)
```

一个类型参数如果出现在多处，那就意味着这些地方的类型必须是同一个：

```crystal
def push(element : T, array : Array(T)) forall T
  array << element
end

push(4, [1, 2, 3]) # OK
push("oops", [1, 2, 3]) # Error
```

