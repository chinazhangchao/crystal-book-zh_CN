# 捕捉块

一个块可以被捕捉，然后成为一个闭包(`Proc`)。相比于块，闭包还包含了它执行所需要的上下文：它包含了一部分数据。

为了捕获一个块，你必须把它列为方法的块参数，赋予名字，并写清楚它的输入和输出类型。例如：

```crystal
def int_to_int(&block : Int32 -> Int32)
  block
end

proc = int_to_int { |x| x + 1 }
proc.call(1) #=> 2
```

上述代码捕获一个闭包，把它作为 `block`传给 `int_to_int`，又把它返回来。这个 `proc`的类型是 [Proc(Int32, Int32)](http://crystal-lang.org/api/Proc.html)即一个函数，输入一个 `Int32`，返回一个 `Int32`.

这样一个块就能作为回调保存：

```crystal
class Model
  def on_save(&block)
    @on_save_callback = block
  end

  def save
    if callback = @on_save_callback
      callback.call
    end
  end
end

model = Model.new
model.on_save { puts "Saved!" }
model.save # prints "Saved!"
```

这个例子中 `&block`的类型没有确定，这只是意味着块不接受参数，也没有返回值。

注意，如果块的返回类型没有确定，那么什么东西都不会返回，即使这个块本来想返回些什么。

```crystal
def some_proc(&block : Int32 ->)
  block
end

proc = some_proc { |x| x + 1 }
proc.call(1) # void
```

为了允许返回值，要么指出返回值的类型，要么留一个下划线，以表示什么返回类型都可以：

```crystal
def some_proc(&block : Int32 -> _)
  block
end

proc = some_proc { |x| x + 1 }
proc.call(1) # 2

proc = some_proc { |x| x.to_s }
proc.call(1) # "1"
```

## break 和 next

`return` 和 `break` 不能在一个捕获了的块中使用。 `next` 仍可以用，他会退出当前块，给出块执行完毕后的值。

## with ... yield

已捕获的块不能用 `with ... yield`改变接收者。
