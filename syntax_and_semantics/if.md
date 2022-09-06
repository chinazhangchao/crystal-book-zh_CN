# if

`if` 当条件是*真的* 时，求值给定的分支。否则，就求值`else`分支(如果有)。

```crystal
a = 1
if a > 0
  a = 10
end
a #=> 10

b = 1
if b > 2
  b = 10
else
  b = 20
end
b #=> 20
```

`elsif`表示 if-else-if 长链：

```crystal
if 某个条件
  做某事
elsif 另一个条件
  做另一些事
else
  其他事
end
```

 `if`之后，一个表达式的类型是他在所有分支中出现的类型之联合。

```crystal
a = 1
if 某个条件
  a = "hello"
else
  a = true
end
# a : String | Bool

b = 1
if 某个条件
  b = "hello"
end
# b : Int32 | String

if 某个条件
  c = 1
else
  c = "hello"
end
# c : Int32 | String

if 某个条件
  d = 1
end
# d : Int32 | Nil
```

注意如果表达式在一个分支中声明，而在另一个分支中没有声明，那么 `if` 之后他会包含 `Nil`类型。

`if`外已有的变量如果在`if`的一个分支中被赋予其他的类型的值，那么他在`if`后就可能是新的类型：

```crystal
a = 1
if 某个条件
  a = "hello"
  # a : String
  a.size
end
# a : String | Int32
```

即，表达式的类型是最后一次赋值的变量的类型。

如果一个分支包含 `return`， `next`， `break` 或 `raise`， 即它绝对不会到达 `if`的结尾，该分支中的赋值就不会影响 `if`后面的变量：

```crystal
if 某个条件
  e = 1
else
  e = "hello"
  # e : String
  return
end
# e : Int32
```
