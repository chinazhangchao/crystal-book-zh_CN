# 块和闭包 

方法可以接受一个程序块，然后用 `yield`关键字执行它。例如：

```crystal
def twice
  yield
  yield
end

twice do
  puts "Hello!"
end
```

上面的例子会打印 "Hello!"两次，每个 `yield`一次。

为了接受一个带有块的方法，只要在方法体中加入 `yield`，然后编译器会明白你的意图。 你可以加入一个块参数来让更明显地表述。块参数应当写在最后，并在名字前面加与号 (`&`)：

```crystal
def twice(&block)
  yield
  yield
end
```

为了向这些方法传入块参数，你可以写 `do ... end` 或是 `{ ... }`，这两种写法等价：

```crystal
twice() do
  puts "Hello!"
end

twice do
  puts "Hello!"
end

twice { puts "Hello!" }
```

`do ... end` 和 `{ ... }` 之间的区别是 `do ... end` 绑定到最左面的调用，而 `{ ... }` 绑定到最右边的调用：

```crystal
foo bar do
  something
end

# 上式等价于
foo(bar) do
  something
end

foo bar { something }

# 上式等价于

foo(bar { something })
```

这样你就可以用`do ... end`创建领域特定语言 (DSL) ，让他们看起来更像英语(或是你的母语)：

```crystal
open file "foo.cr" do
  something
end

# 等同于:
open(file("foo.cr")) do
end
```

你不会喜欢写：

```crystal
open(file("foo.cr") do
end)
```

## 对于重载

如果两个方法名字相同，一个接受块，一个不接受，那么它们会被认为是两个不同的方法。详见[重载](overloading.html) 一章。

## 向块传递参数

`yield` 表达式类似于调用，也可以接收参数。例如：

```crystal
def twice
  yield 1
  yield 2
end

twice do |i|
  puts "Got #{i}"
end
```

它会打印 "Got 1" 和 "Got 2"。

也可以用花括号：

```crystal
twice { |i| puts "Got #{i}" }
```

可以 `yield`多个值：

```crystal
def many
  yield 1, 2, 3
end

many do |x, y, z|
  puts x + y + z
end

# 输出: 6
```

一个块可以不完全接受这些参数：

```crystal
def many
  yield 1, 2, 3
end

many do |x, y|
  puts x + y
end

# 输出: 3
```

但如果接受超出了传送额缝纫参数，那就会产生错误：

```crystal
def twice
  yield
  yield
end

twice do |i| # 错误: 块要求参数过多
end
```

每个块内变量的类型是该位置传进来所有变量类型的并。例如：

```crystal
def some
  yield 1, 'a'
  yield true, "hello"
  yield 2, nil
end

some do |first, second|
  # first is Int32 | Bool
  # second is Char | String | Nil
end
```

快变量 `second` 也可以是 `Nil` ，因为最后一个 `yield` 表达式没有包含第二个参数。

## 单参数块的简便写法

如果某个块只是接受一个参数，调用它的某个方法，那么有一个简便的方法表示它：

```crystal
method do |argument|
  argument.some_method
end
```

可以这么写：

```crystal
method &.some_method
```

或是：

```crystal
method(&.some_method)
```

这只是个语法糖，不会产生性能损失。

也可以把参数传给 `some_method`：

```crystal
method &.some_method(arg1, arg2)
```

这也适用于运算符：

```crystal
method &.+(2)
method &.[index]
```

## 产出值

`yield` 表达式本身也有值：块中的最后一个表达式的值。例如：

```crystal
def twice
  v1 = yield 1
  puts v1

  v2 = yield 2
  puts v2
end

twice do |i|
  i + 1
end
```

上式会打印 "2" 和 "3"；

`yield`表达式在变换换筛选值时尤其有用。最好的例子是 [Enumerable#map](http://crystal-lang.org/api/Enumerable.html#map%28%26block%20%3A%20T%20-%3E%20U%29-instance-method) 和[Enumerable#select](http://crystal-lang.org/api/Enumerable.html#select%28%26block%20%3A%20T%20-%3E%20%29-instance-method)：

```crystal
ary = [1, 2, 3]
ary.map { |x| x + 1 }         #=> [2, 3, 4]
ary.select { |x| x % 2 == 1 } #=> [1, 3]
```

有一个形式上的转换方法：

```crystal
def transform(value)
  yield value
end

transform(1) { |x| x + 1 } #=> 2
```

最后一个表达式的结果是 `2`，因为 `transform`方法的最后一个表达式是 `yield`，它的值又是块的最后一个表达式。

## 类型限制

使用 `yield` 的方法中，块的类型可以用 `&block`的属性进行限制。例如：

```crystal
def transform_int(start : Int32, &block : Int32 -> Int32)
  result = yield start
  result * 2
end

transform_int(3) { |x| x + 2 } #=> 10
transform_int(3) { |x| "foo" } # 错误：块应该返回Int32，而不是String 
```

## break

块内的`break` 表达式可以提前跳出这个方法：

```crystal
def thrice
  puts "Before 1"
  yield 1
  puts "Before 2"
  yield 2
  puts "Before 3"
  yield 3
  puts "After 3"
end

thrice do |i|
  if i == 2
    break
  end
end
```

上述程序打印 "Before 1"和 "Before 2"。这个 `thrice` 方法没有执行到 `puts "Before 3"`，因为 `break`让它早早地退出了。

`break` 也可以接受参数，它将成为该方法的返回值。例如：

```crystal
def twice
  yield 1
  yield 2
end

twice { |i| i + 1 } #=> 3
twice { |i| break "hello" } #=> "hello"
```

第一次方法调用的值是 3 ，因为 `twice`方法的最后一个表达式是 `yield`，它又从块中获得值。第二次调用的值是 "hello"，因为 `break`被执行了，它的参数成了返回值。

如果break在条件内，那么这个调用的返回类型会是块中所有可能返回的地方——比如`break`和最后一个表达式——的值类型的联合：
```crystal
value = twice do |i|
  if i == 1
    break "hello"
  end
  i + 1
end
value #:: Int32 | String
```

如果`break` 接受了多个参数，那么它们自动组成一个 [元组](http://crystal-lang.org/api/Tuple.html):

```crystal
values = twice { break 1, 2 }
values #=> {1, 2}
```

如果`break` 没有收到参数，那么等同于接受一个 `nil`：

```crystal
value = twice { break }
value #=> nil
```

## next

`next`表达式可以提前结束这个块 (而不是整个方法)。例如：

```crystal
def twice
  yield 1
  yield 2
end

twice do |i|
  if i == 1
    puts "Skipping 1"
    next
  end

  puts "Got #{i}"
end

# 输出:
# Skipping 1
# Got 2
```

`next` 表达式接收参数，这会传给`yield`的调用者：

```crystal
def twice
  v1 = yield 1
  puts v1

  v2 = yield 2
  puts v2
end

twice do |i|
  if i == 1
    next 10
  end

  i + 1
end

# 输出
# 10
# 3
```

如果 `next`接收了多个参数，它们会自动组成一个 [元组](http://crystal-lang.org/api/Tuple.html)。如果没有接收到值，那么等同于接受一个 `nil` 参数。

## with ... yield

`yield`表达式可以用 `with` 关键字指定块中方法调用的接收者：

```crystal
class Foo
  def one
    1
  end

  def yield_with_self
    with self yield
  end

  def yield_normally
    yield
  end
end

def one
  "one"
end

Foo.new.yield_with_self { one } # => 1
Foo.new.yield_normally { one }  # => "one"
```

## 块参数解包

声明绿参数时可以用括号指定子参数：

```crystal
array = [{1, "one"}, {2, "two"}]
array.each do |(number, word)|
  puts "#{number}: #{word}"
end
```

这是下面这种写法的语法糖：

```crystal
array = [{1, "one"}, {2, "two"}]
array.each do |arg|
  number = arg[0]
  word = arg[1]
  puts "#{number}: #{word}"
end
```

这也意味着任何可以以整数为参数响应 `[]` 方法的对象都可以在这里解包。

## 性能

当以 `yield`使用块时，这个块 **总是** 被内联：不会涉及闭包，调用，或是函数指针。这意味着：

```crystal
def twice
  yield 1
  yield 2
end

twice do |i|
  puts "Got: #{i}"
end
```

等同于写：

```crystal
i = 1
puts "Got: #{i}"
i = 2
puts "Got: #{i}"
```

例如，标准库对整数添加了 `times` 方法，你可以写：

```crystal
3.times do |i|
  puts i
end
```

这看起来非常花哨，但是它确实和C的循环一样快，真的。

这是 `Int#times` 的定义：

```crystal
struct Int
  def times
    i = 0
    while i < self
      yield i
      i += 1
    end
  end
end
```

因为没有被捕获的块总是被内联，上面的方法调用 **等同于** 写：

```crystal
i = 0
while i < 3
  puts i
  i += 1
end
```

使用块不用担心它的性能问题，但确实能提升代码可读性和重用性。
