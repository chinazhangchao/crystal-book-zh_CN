# instance_sizeof

`instance_sizeof` 表达式返回一个 `Int32`，表示这个类实例的空间大小。例如：

```crystal
class Point
  def initialize(@x, @y)
  end
end

Point.new 1, 2

# 2 x Int32 = 2 x 4 = 8
instance_sizeof(Point) #=> 12
```

即使这个实例只有两个 `Int32`字段，编译器总会添加一个额外的 `Int32` 字段，表示这个对象的类型 id。因此这个实例的大小是12，而不是8。
