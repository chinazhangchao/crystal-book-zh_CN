# 闭包(Proc)

[闭包](http://crystal-lang.org/api/Proc.html) 表示带有可选上下文的函数指针，通常以闭包字面量构造：
```crystal
# 无参数闭包
->{ 1 } # Proc(Int32)

# 有一个参数的闭包
->(x : Int32) { x.to_s } # Proc(Int32, String)

# 有两个参数的闭包
->(x : Int32, y : Int32) { x + y } # Proc(Int32, Int32, Int32)
```

参数的类型是必须的，除非把闭包字面量直接送给C绑定中的一个库 `fun`。

返回值可以从闭包体推断出来。

另外，闭包还有一个 `new`方法：

```crystal
Proc(Int32, String).new { |x| x.to_s } # Proc(Int32, String)
```

这可以直接指定返回值，并且和闭包体的返回值比对。

## 闭包类型

你可以这样声明一个闭包类型：

```crystal
# 闭包接受一个 Int32 参数，返回一个 String
Proc(Int32, String)

# 闭包不接收参数，返回 Void
Proc(Void)

# 闭包接受两个参数 (一个 Int32 和一个 String) ，返回一个 Char
Proc(Int32, String, Char)
```

在类型限制，泛型参数和其他需要类型的地方，你可以用一个简短的语法，详见[类型](../type_grammar.html):

```crystal
# 一个由 Proc(Int32, String, Char)构成的 Array :
Array(Int32, String -> Char)
```

## 调用

你可以用 `call`方法调用闭包参数的个数必须匹配闭包的类型：

```crystal
proc = ->(x : Int32, y : Int32) { x + y }
proc.call(1, 2) #=> 3
```

## 从方法构造闭包

闭包可以从已有的方法构造：

```crystal
def one
  1
end

proc = ->one
proc.call #=> 1
```

如果方法有参数，你必须确定它们的类型：

```crystal
def plus_one(x)
  x + 1
end

proc = ->plus_one(Int32)
proc.call(41) #=> 42
```
闭包可以可选地指定一个方法接受者：

```crystal
str = "hello"
proc = ->str.count(Char)
proc.call('e') #=> 1
proc.call('l') #=> 2
```
