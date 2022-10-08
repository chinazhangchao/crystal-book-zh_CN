# pointerof

表达式 `pointerof` 返回指向这个变量的[指针](http://crystal-lang.org/api/Pointer.html)。

对变量的例子：

```crystal
a = 1

ptr = pointerof(a)
ptr.value = 2

a #=> 2
```

对实例变量的例子：

```crystal
class Point
  def initialize(@x : Int32, @y : Int32)
  end

  def x
    @x
  end

  def x_ptr
    pointerof(@x)
  end
end

point = Point.new 1, 2

ptr = point.x_ptr
ptr.value = 10

point.x #=> 10
```

因为 `pointerof` 涉及指针操作，它被认为是 [不安全](unsafe.html)的。

