# 运算符

 `+` 和 `-` 这样的运算符也是方法调用。例如：

```crystal
a + b
```

等同于

```crystal
a.+(b)
```

你可以如此定义一个运算符：

```crystal
struct Vector2
  getter x, y

  def initialize(@x : Int32, @y : Int32)
  end

  def +(other)
    Vector2.new(x + other.x, y + other.y)
  end
end

v1 = Vector2.new(1, 2)
v2 = Vector2.new(3, 4)
v1 + v2               #=> Vector2(@x=4, @y=6)
```

下面展示了所有的运算符及它们的意义。

## 单目运算符

```crystal
+   # 正号
-   # 负号
!   # 逻辑非
~   # 按位取反
```

它们的定义不带参数。例如：

```crystal
struct Vector2
  def -
    Vector2.new(-x, -y)
  end
end

v1 = Vector2.new(1, 2)
-v1                    #=> Vector2(@x=-1, @y=-2)
```

**注意:** `!` (非) 不能被定义为方法，即它的意义不能被改变。

## 双目运算符

* `+` – 加法
* `-` – 减法
* `*` – 乘法
* `/` – 除法
* `%` – 取模
* `&` – 位与
* `|` – 位或
* `^` – 位异或
* `**` – 指数
* `<<` – 左移，添加
* `>>` – 右移
* `==` – 等于
* `!=` – 不等于
* `<` – 小于
* `<=` – 小于等于
* `>` – 大于
* `>=` – 大于等于
* `<=>` – 三相比较
* `===` – [匹配](case.html)

## 索引

```crystal
[]  # 数组/散列索引 (越界时抛出异常)
[]? # 数组/散列索引 (越界时返回nil)
[]= # 数组/散列索引赋值
```

例如：

```crystal
class MyArray
  def [](index)
    # ...
  end

  def [](index1, index2, index3)
    # ...
  end

  def []=(index, value)
    # ...
  end
end

array = MyArray.new

array[1]       # 调用第一个方法
array[1, 2, 3] # 调用第二个方法
array[1] = 2   # 调用第三个方法

array.[](1)       # 调用第一个方法
array.[](1, 2, 3) # 调用第二个方法
array.[]=(1, 2)   # 调用第三个方法
```

## 意义

你可以为运算符指定任何意义。但是为了代码可读性或者可解释性，你应当遵守上述约定。
