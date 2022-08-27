# 赋值

等号 (`=`) 表示赋值操作。

```crystal
# 向局部变量赋值
local = 1

# 向实例变量赋值
@instance = 2

# 向类变量赋值
@@class = 3
```

上述三种赋值操作将随后解释。

有一些包含 `=`的语法糖也可用于赋值：

```crystal
local += 1  # 等同于: local = local + 1

# 上述操作可用于这些运算符：
# +, -, *, /, %, |, &, ^, **, <<, >>

local ||= 1 # 等同于: local || (local = 1)
local &&= 1 # 等同于: local && (local = 1)
```

以 `=`结尾的方法有语法糖：

```crystal
# setter
person.name=("John")

# 上式等同于：
person.name = "John"

# 下标寻址加赋值
objects.[]=(2, 3)

# 上式等同于：
objects[2] = 3

# 并不是赋值，但也有语法糖
objects.[](2, 3)

# 上式等同于：
objects[2, 3]
```

`=` 运算符对设置和索引操作也有语法糖。注意， `||` 和 `&&` 使用 `[]?` 方法检查键是否存在。

```crystal
person.age += 1        # 等同于: person.age = person.age + 1

person.name ||= "John" # 等同于: person.name || (person.name = "John")
person.name &&= "John" # 等同于: person.name && (person.name = "John")

objects[1] += 2        # 等同于: objects[1] = objects[1] + 2

objects[1] ||= 2       # 等同于: objects[1]? || (objects[1] = 2)
objects[1] &&= 2       # 等同于: objects[1]? && (objects[1] = 2)
```
