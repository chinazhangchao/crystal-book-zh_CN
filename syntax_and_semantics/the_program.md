# Crystal 程序

Crystal 程序是一个全局对象，在里面可以定义类型、方法和文件局部变量。

```crystal
# 在程序定义方法
def add(x, y)
  x + y
end

# 在程序调用add方法
add(1, 2) #=> 3
```

方法的返回值是最后一条表达式的值；无需显式使用 `return` 表达式。当然也可以使用 `return` ：

```crystal
def even?(num)
  if num % 2 == 0
    return true
  end

  return false
end
```

当调用一个方法时，假如没有指定接收者，例如 `add(1, 2)`，如果在当前类型或祖先链中没有找该方法，就会在全局程序中继续查找。

```crystal
def add(x, y)
  x + y
end

class Foo
  def bar
    # 调用全局程序的 add 方法
    add(1, 2)

    # 调用 Foo 的 baz 方法
    baz(1, 2)
  end

  def baz(x, y)
    x * y
  end
end
```

如果要调用全局程序的方法，不管当前类型是否定义了一个同名方法，都可以使用前缀 `::`：

```crystal
def baz(x, y)
  x + y
end

class Foo
  def bar
    baz(4, 2) #=> 2
    ::baz(4, 2) #=> 6
  end

  def baz(x, y)
    x - y
  end
end
```

全局声明的变量在方法内不可见：

```crystal
x = 1

def add(y)
  x + y # error: undefined local variable or method 'x'
end

add(2)
```

方法调用时圆括号可以省略：

```crystal
add 1, 2 # 等价于 add(1, 2)
```

## 主代码

主代码是编译运行程序时运行的代码，可以直接在源文件里写，无需放入 "main" 函数：

```crystal
# 这是一个输出 "Hello Crystal!" 的程序
puts "Hello Crystal!"
```

主代码也可以在类型声明里：

```crystal
# 这是一个输出 "Hello" 的程序
class Hello
  # 这里的 'self' 是 Hello 类
  puts self
end
```
