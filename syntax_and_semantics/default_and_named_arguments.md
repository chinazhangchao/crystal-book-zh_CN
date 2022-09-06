# 默认值

一个方法可以指定后面一些参数的默认值：

```crystal
class Person
  def become_older(by = 1)
    @age += by
  end
end

john = Person.new "John"
john.age #=> 0

john.become_older
john.age #=> 1

john.become_older 2
john.age #=> 3
```

# 命名参数

所有的参数除了使用位置确定之外，还可以通过名称来确定。例如：

```crystal
john.become_older by: 5
```

当有多个参数时，只要所有需要的参数都指定了，那么指定名称的顺序就没有影响。

```crystal
def some_method(x, y = 1, z = 2, w = 3)
  # 做一些事情
end

some_method 10                   # x: 10, y: 1, z: 2, w: 3
some_method 10, z: 10            # x: 10, y: 1, z: 10, w: 3
some_method 10, w: 1, y: 2, z: 3 # x: 10, y: 2, z: 3, w: 1
some_method y: 10, x: 20         # x: 20, y: 10, z: 2, w: 3

some_method y: 10                # 错误: 缺少参数 x
```

当用元组概括不定数量参数时，不能使用命名参数。因为这时难以确定哪个输入匹配哪个位置。此时位置参数相对易于理解。
