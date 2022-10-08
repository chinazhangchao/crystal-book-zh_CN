# 结构体(Structs)

除了用 `class`定义类型，你还可以用 `struct`：

```crystal
struct Point
  property x, y

  def initialize(@x : Int32, @y : Int32)
  end
end
```

结构体和类的区别是：
* 对结构体调用 `new` 会把它分配到栈上(而不是堆上)
* 结构体 [按值传递](http://crystal-lang.org/api/Value.html),而类按引用传递
* 结构体继承 [Struct](http://crystal-lang.org/api/Struct.html)，而`Struct`又继承 [Value](http://crystal-lang.org/api/Value.html)。类隐式地继承 [Reference](http://crystal-lang.org/api/Reference.html)。
* 结构体不能继承一个非抽象的结构体。

最后一点原因在于：结构体有准确的内存布局。例如，上面的 `Point` 占有 8字节。如果你有一个 points 数组，那么这些点就会被直接嵌到这个数组的空间中：

```crystal
# 数组给每个Point分配8字节
ary = [] of Point
```

如果 `Point` 被继承，那么这个类型的数组必须知道它里面可能有类型的对象，所以每个元素的大小必须增长，以容纳这些对象，而这不是我们期望的。因此，非抽象的结构体不能被继承。对应地，抽象的结构体可以有子类，一个由它们构成的数组可以考虑其中所有可能的类型。

像类一样，结构体也可以包含模块，也可以是泛型。

结构体常用于优化性能：如果对象按值传递的代价也不大，那么直接在栈上分配它们可以减少频繁分配内存的损耗。

所以你要如何选择定义结构体还是类？凭经验来说，如果没有成员变量被重新赋值，即，你的类型是不可变的，那就定义成结构体；反之则定义成类。
