# 闭包及其环境变量 (Closures)

捕获的块会和闭包字面量会把局部变量和 `self` 包含在自身环境内。举例子比较好理解：

```crystal
x = 0
proc = ->{ x += 1; x }
proc.call #=> 1
proc.call #=> 2
x         #=> 2
```

或者是从方法中返回的闭包：

```crystal
def counter
  x = 0
  ->{ x += 1; x }
end

proc = counter
proc.call #=> 1
proc.call #=> 2
```

上例中，即使 `x`是局部变量，他也会被闭包捕获。这种情况编译器会把 `x`分配在堆上，用作闭包的环境信息。因为普通的局部变量分配在栈上，方法返回时它们就消失了。

## Type of closured variables

编译器处理一般的局部变量是比较聪明的。例如：

```crystal
def foo
  yield
end

x = 1
foo do
  x = "hello"
end
x # : Int32 | String
```

编译器知道，在这个块执行之后， `x` 可以是 Int32 或 String (它本可以知道这一定是 String，因为这个方法总是执行传进来的块。 这个推断以后还能更精准)。

如果 `x` 被赋值成其他的变量，编译器会知道它被改变了：

```crystal
x = 1
foo do
  x = "hello"
end
x # : Int32 | String

x = 'a'
x # : Char
```

然而，如果 `x` 被一个闭包捕获，它的类型就一定是所有赋值给它的类型之并：

```crystal
def capture(&block)
  block
end

x = 1
capture { x = "hello" }

x = 'a'
x # : Int32 | String | Char
```

这是因为捕获的块可能被存在类或是成员变量中，然后按照某个指令被另一个线程调用。编译器不会再穷尽分析下去，它只会认为如果一个变量被闭包捕获，比宝贝执行的时间就不确定。

这也适用于常规的闭包字面量，即使这个闭包显然不会被储存或调用：

```crystal
def capture(&block)
  block
end

x = 1
->{ x = "hello" }

x = 'a'
x # : Int32 | String | Char
```



