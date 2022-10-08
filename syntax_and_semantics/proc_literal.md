# 闭包字面量

一个捕获块等同于声明一个 [闭包字面量](literals/proc.html) ，然后把它[传递](block_forwarding.html) 给一个方法。

```crystal
def some_proc(&block : Int32 -> Int32)
  block
end

x = 0
proc = ->(i : Int32) { x += i }
proc = some_proc(&proc)
proc.call(1)  #=> 1
proc.call(10) #=> 11
x #=> 11
```

如[闭包字面量](literals/proc.html)一篇所述，闭包也可以由已有方法构造：

```crystal
def add(x, y)
  x + y
end

adder = ->add(Int32, Int32)
adder.call(1, 2) #=> 3
```
