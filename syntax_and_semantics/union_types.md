# 联合类型

变量和表达式的类型可以有多个类型组成，这被称为联合类型。例如，当在 [if](if.html)的不同分支中赋值同一个变量时：

```crystal
if 1 + 2 == 3
  a = 1
else
  a = "hello"
end

a # : Int32 | String
```

在if的结尾， `a` 会有类型 `Int32 | String`，读作 " Int32 和 String的联合类型"。这个联合类型由编译器自动构建。在运行时， `a` 当然只会有一个类型。这可以由调用 `class`方法看出：

```crystal
# 运行时类型
a.class # => Int32
```

编译时类型可以由 [typeof](typeof.html)看出：

```crystal
# 编译时类型
typeof(a) # => Int32 | String
```

一个联合类型可以由任意多的类型组成。当调用联合类型的某个方法时，所有的类型都必须响应这个方法，否则会产生编译错误。这个方法调用返回值的类型是所有可能的返回值类型的联合。

```crystal
# to_s 在 Int32 和 String 都有定义，它返回 String
a.to_s # => String

a + 1 # 错误，因为 String#+(Int32) 没有定义
```

如果需要，一个变量可以在编译时设为联合类型。

```crystal
# 设置编译时类型
a = 0.as(Int32|Nil|String)
typeof(a) # => Int32 | Nil | String
```

## 联合类型规则

一般来说，但两个类型 `T1`和 `T2`结合时，得到的类型是 `T1 | T2`。然而，在少数情况返回值可以是其他的类型。

### 同一级的类和结构体之联合

如果 `T1` 和 `T2`在同一个等级，并且它们最近的共同祖先 `Parent`不是 `Reference`、 `Struct`、 `Int`、 `Float`或 `Value`，则返回类型是 `Parent+`。这称作虚类型，意思是编译器不会把它看做 `Parent`，或是任何的子类型。

例如：

```crystal
class Foo
end

class Bar < Foo
end

class Baz < Foo
end

bar = Bar.new
baz = Baz.new

# 这里 foo的类型是 Bar | Baz,
# 但是由于Bar 和 Baz 都继承 Foo,
# 返回类型是 Foo+
foo = rand < 0.5 ? bar : baz
typeof(foo) # => Foo+
```

### 同样大小的元组之联合

同样大小的元组的联合，会得到一个新元组，每个位置的类型是原先对应位置的各类型之并。

例如：

```crystal
t1 = {1, "hi"}   # Tuple(Int32, String)
t2 = {true, nil} # Tuple(Bool, Nil)

t3 = rand < 0.5 ? t1 : t2
typeof(t3) # Tuple(Int32 | Bool, String | Nil)
```

### 相同键名的命名元组之联合

不管键的排列顺序如何，相同键名的命名元组之联合会得到一个新元组，每个键值的类型是原先对应键值的各类型之并。键的顺序如同最表达式左边的那个元组。

例如：

```crystal
t1 = {x: 1, y: "hi"}   # Tuple(x: Int32, y: String)
t2 = {y: true, x: nil} # Tuple(y: Bool, x: Nil)

t3 = rand < 0.5 ? t1 : t2
typeof(t3) # NamedTuple(x: Int32 | Nil, y: String | Bool)
```
