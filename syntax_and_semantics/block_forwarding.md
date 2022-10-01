# 传递块

为了把代码块传给其他方法，你应当注明块参数，在其前面加 `&`：

```crystal
def capture(&block)
  block
end

def invoke(&block)
  block.call
end

proc = capture { puts "Hello" }
invoke(&proc) # 打印 "Hello"
```

上例中， `invoke` 接收块，我们不能把闭包直接传进它，因为它不接受一般的参数，只接受块参数。我们用 `&` 指定“我们真的要把 `proc`当做块参数传进去”，否则会：

```crystal
invoke(proc) # 错误: 'invoke' 参数数量匹配 (给出 1, 应有 0)
```

你可以把闭包传给使用 yield 的方法：

```crystal
def capture(&block)
  block
end

def twice
  yield
  yield
end

proc = capture { puts "Hello" }
twice &proc
```

上述等同于：

```crystal
proc = capture { puts "Hello" }
twice do
  proc.call
end
```

或者，结合 `&` 和 `->` 的句法：

```crystal
twice &->{ puts "Hello" }
```

Or:

```crystal
def say_hello
  puts "Hello"
end

twice &->say_hello
```

## 传递非捕获的块

要传递非捕获的块，你必须用 `yield`：

```crystal
def foo
  yield 1
end

def wrap_foo
  puts "Before foo"
  foo do |x|
    yield x
  end
  puts "After foo"
end

wrap_foo do |i|
  puts i
end

# 输出:
# Before foo
# 1
# After foo
```

You can also use the `&block` syntax to forward blocks, but then you have to at least specify the input types, and the generated code will involve closures and will be slower:

```crystal
def foo
  yield 1
end

def wrap_foo(&block : Int32 -> _)
  puts "Before foo"
  foo(&block)
  puts "After foo"
end

wrap_foo do |i|
  puts i
end

# Output:
# Before foo
# 1
# After foo
```

Try to avoid forwarding blocks like this if doing `yield` is enough. There's also the issue that `break` and `next` are not allowed inside captured blocks, so the following won't work when using `&block` forwarding:

```crystal
foo_forward do |i|
  break # error
end
```

In short, avoid `&block` forwarding when `yield` is involved.
