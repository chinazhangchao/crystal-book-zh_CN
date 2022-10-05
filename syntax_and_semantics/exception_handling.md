# 异常处理

Crystal处理错误的一般方法就是通过抛出和处理异常。


## 抛出异常

你通过调用顶层的 `raise` 方法来抛出异常。 `raise`不是关键字，而只是正常的方法。它有两个重载，一个接受 [字符串](http://crystal-lang.org/api/toplevel.html#raise%28message%20%3A%20String%29-class-method) ，另一个接受 [ Exception 实例](http://crystal-lang.org/api/toplevel.html#raise%28ex%20%3A%20Exception%29-class-method)：

```crystal
raise "OH NO!"
raise Exception.new("Some error")
```

字符串版本只是用它的消息创建了一个新的 [Exception](http://crystal-lang.org/api/Exception.html) 实例。

只有 `Exception`及其子类的实例可以被抛出。

## 自定义异常

你可以让你的类继承 [Exception](http://crystal-lang.org/api/Exception.html)，这样它就会被视为一个异常：

```crystal
class MyException < Exception
end

class MyOtherException < Exception
end
```

你可以，并且总是可以，给你的异常定义构造函数，或使用默认的那个。

## 抢救异常

用 `begin ... rescue ... end` 表达式抢救异常：

```crystal
begin
  raise "OH NO!"
rescue
  puts "Rescued!"
end

# 输出: Rescued!
```

在 `rescue`子句中添加变量，以获取抛出的异常:

```crystal
begin
  raise "OH NO!"
rescue ex
  puts ex.message
end

# 输出: OH NO!
```

抢救某一类型的异常 (或它的任意子类)：

```crystal
begin
  raise MyException.new("OH NO!")
rescue MyException
  puts "抢救了 MyException"
end

# 输出: 抢救了 MyException
```

为了取得它的实例，你可以用类型限制的语法标注它：

```crystal
begin
  raise MyException.new("OH NO!")
rescue ex : MyException
  puts "抢救了 MyException: #{ex.message}"
end

# 输出: 抢救了 MyException: OH NO!
```

这个结构可以接多个 `rescue`子句：

```crystal
begin
  # ...
rescue ex1 : MyException
  # 只接受 MyException...
rescue ex2 : MyOtherException
  # 只接受 MyOtherException...
rescue
  # 其他任意异常
end
```

你也可以用联合类型在一个字句中接受多个异常。

```crystal
begin
  # ...
rescue ex : MyException | MyOtherException
  # 接受 MyException 或 MyOtherException
rescue
  # 其他任意异常
end
```

## else

这个结构可以接 `else` ，它会在没有任何异常被捕获的时候被执行：

```crystal
begin
  something_dangerous
rescue
  # 如果有异常抛出，就执行
else
  # 如果没有异常抛出，就执行
end
```

`else` 子句只能在指定了至少一个 `rescue` 子句后使用。

## ensure

`ensure`子句接在 `begin ... end` 或 `begin ... rescue ... end` 表达式的后方，保证它的内容被执行，不论是否有异常抛出：

```crystal
begin
  something_dangerous
ensure
  puts "Cleanup..."
end

# 会在调用 something_dangerous 之后执行 "Cleanup..." ,
# 不论是否有异常抛出
```

或者：

```crystal
begin
  something_dangerous
rescue
  # ...
else
  # ...
ensure
  # 这总是会被执行
end
```

`ensure` 通常用于清理现场，释放资源，等等。

## 简短格式

异常处理有一个简短的格式：所有的方法或块定义可以视为隐含着一个 `begin ... end`表达式，所以可以直接指定 `rescue`， `else`，和 `ensure` 子句：

```crystal
def some_method
  something_dangerous
rescue
  # 当有异常抛出时执行
end

# 上式等同于：
def some_method
  begin
    something_dangerous
  rescue
    # 当有异常抛出时执行
  end
end
```

加`ensure`：

```crystal
def some_method
  something_dangerous
ensure
  # 总是执行
end

# 上式等同于:
def some_method
  begin
    something_dangerous
  ensure
    # 总是执行
  end
end

# 类似的，这种简便写法也适用于块：
(1..10).each do |n|
  # 潜在有危险的操作
rescue
  #..
else
  #..
ensure
  #..
end
```

## 类型推导

`begin`中定义的变量在 `rescue` 或 `ensure`中可能是 `Nil` 类型。例如：

```crystal
begin
  a = something_dangerous_that_returns_Int32
ensure
  puts a + 1 # 错误， Nil 没有定义 '+' 方法 
end
```

即使 `something_dangerous_that_returns_Int32` 从不抛异常，这个错误也会发生。或者，如果 `a` 被赋以一个变量，后面又做了可能抛异常的操作，这种错误也会发生：

```crystal
begin
  a = 1
  something_dangerous
ensure
  puts a + 1 # 错误， Nil 没有定义 '+' 方法 
end
```

即使 `a` 显然总是有被赋予一个值，编译器仍然很认为也许`a` 来不及初始化，程序就跑到了其他的地方。这种逻辑以后也许会被修复，但现在你最好把异常处理程序限制在最小的规模，这样程序的意图也会更清晰。

```crystal
#这比上面的清晰： `a` 不需要放在异常处理代码中
a = 1
begin
  something_dangerous
ensure
  puts a + 1 # 能跑
end
```

## 错误处理的另一种方法

尽管异常是一种处理错误的方法，但它不是唯一的方法。抛出异常意味着消耗内存，执行异常处理往往会减慢程序速度。

标准库通常提供一堆方法去做某事。出错时，它们一个抛出异常，另一个返回 `nil`。例如：

```crystal
array = [1, 2, 3]
array[4]  # 抛出 IndexError
array[4]? # 返回 nil ，因为下标越界了
```

通常的约定是，一个以问号结尾的方法会在出错时返回 `nil`，而不是抛出异常。这样用户就可以决定他是否要处理异常，或是得到一个  `nil`。然而，这不只是对所有方法都如此。毕竟，异常仍然是推荐的处理，因为它不会污染正常代码的逻辑。 
