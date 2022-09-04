# 元组展开

一个方法可以通过*星号参数* (`*`)来接收不定数量的参数。星号参数可以出现在任何位置，但只能出现一次：

```crystal
def sum(*elements)
  total = 0
  elements.each do |value|
    total += value
  end
  total
end

sum 1, 2, 3    #=> 6
sum 1, 2, 3, 4.5 #=> 10.5
```

这些传递的参数在消息内会形成一个[元组](http://crystal-lang.org/api/Tuple.html)：

```crystal
# 元组(Int32, Int32, Int32)中的元素
sum 1, 2, 3

# 元组(Int32, Int32, Int32, Float64)中的元素
sum 1, 2, 3, 4.5
```

星号后面只能接命名参数：

```crystal
def sum(*elements, initial = 0)
  total = initial
  elements.each do |value|
    total += value
  end
  total
end

sum 1, 2, 3 # => 6
sum 1, 2, 3, initial: 10 # => 16
```

星号后面的参数如果没有默认值，那就只能用名称指定：

```crystal
def sum(*elements, initial)
  total = initial
  elements.each do |value|
    total += value
  end
  total
end

sum 1, 2, 3 # 错误，缺少参数: initial
sum 1, 2, 3, initial: 10 # => 16
```

两个需要不同名称的命名参数是不同的,只是重载在一起而已：

```crystal
def foo(*elements, x)
  1
end

def foo(*elements, y)
  2
end

foo x: "something" # => 1
foo y: "something" # => 2
```

星号参数也可以不写名字，这意味着“我之后只能是命名参数”：

```crystal
def foo(x, y, *, z)
end

foo 1, 2, 3    # 错误, 参数数目不匹配 (给出 3, 期待 2)
foo 1, 2       # 错误, 缺少参数: z
foo 1, 2, z: 3 # OK
```

## 展开元祖

`Tuple` 可以用`*`在方法中展开：

```crystal
def foo(x, y)
  x + y
end

tuple = {1, 2}
foo *tuple # => 3
```

## 双星号和命名元组

双星号(`**`) 接受没有被其他名称匹配的命名参数。他自己的类型是 `NamedTuple`：

```crystal
def foo(x, **other)
  # 以 NamedTuple 的形式返回其他参数
  other
end

foo 1, y: 2, z: 3    # => {y: 2, z: 3}
foo y: 2, x: 1, z: 3 # => {y: 2, z: 3}
```

## 命名元组的展开

`NamedTuple` 可以通过 `**`展开，送进一个方法：

```crystal
def foo(x, y)
  x - y
end

tuple = {y: 3, x: 10}
foo **tuple # => 7
```
