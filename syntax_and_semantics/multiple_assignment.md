# 多重赋值

可以通过用逗号(`,`) 分隔的一系列表达式进行多重声明/赋值：

```crystal
name, age = "Crystal", 1

# 上式等同于:
temp1 = "Crystal"
temp2 = 1
name  = temp1
age   = temp2
```

注意，因为表达式会被赋值给临时变量，你可以通过这种方式在一行内交换两个变量的值：

```crystal
a = 1
b = 2
a, b = b, a
a #=> 2
b #=> 1
```

如果右边只有一个表达式，它会被认为是一个可索引的类型，因此有如下语法糖：

```crystal
name, age, source = "Crystal,1,github".split(",")

# 上式等同于:
temp = "Crystal,1,github".split(",")
name   = temp[0]
age    = temp[1]
source = temp[2]
```

以 `=`结尾的方法也可以用于多重赋值：

```crystal
person.name, person.age = "John", 32

# 等同于:
temp1 = "John"
temp2 = 32
person.name = temp1
person.age = temp2
```

索引操作也同样适用 (`[]=`)：

```crystal
objects[1], objects[2] = 3, 4

# 等同于:
temp1 = 3
temp2 = 4
objects[1] = temp1
objects[2] = temp2
```
