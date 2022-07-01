# 哈希(Hash)

[哈希](http://crystal-lang.org/api/Hash.html) 是从键类型`K`到值类型`V`的映射的泛型。

构造哈希的最简单方法是哈希字面量。它由大括号(`{ }`)包含键值对构成，每个键值对是`a=>b`的形式，中间以逗号`,`分隔。
```crystal
{"one" => 1, "two" => 2}
```

# 泛型参数

键 `K` 和值 `V` 的泛型参数 可以从字面量从元素的类型推导得来，如果键和值的类型分别都相同，那么`K`/`V` 就是那个类型。否则它就分别是里面出现过的所有类型的并。

```crystal
{1 => 2, 3 => 4}     # Hash(Int32, Int32)
{1 => 2, 'a' => 3}   # Hash(Int32 | Char, Int32)
```

可以用一对空花括号接 `of` (用空格隔开), 一个类型 (`K`) 接 `=>` 接另一个类型 (`V`)来显式地指明类型. 这会覆盖推断出来的类型。这可以使哈希一开始只有一些一种类型的元素，但后来也可以添加其他类型的元素。

空哈希字面量总是需要类型标注：
```crystal
{} of Int32 => Int32 # => Hash(Int32, Int32).new
```

## 哈希式类型字面量

Crystal 对哈希和类似哈希的类型有字面量支持。它由类型接以花括号(`{}`)包住的，用逗号分隔的键值对。

```crystal
Hash{"one" => 1, "two" => 2}
```

这对有空参数构造函数和 `[]=` 方法的类型都适用。

```crystal
HTTP::Headers{"foo" => "bar"}
```

对于 `HTTP::Headers`这样的非泛型，这等价于：

```crystal
headers = HTTP::Headers.new
headers["foo"] = "bar"
```

对于泛型，它其中的类型用类似于哈希字面量的方法推断出来。

```crystal
MyHash{"foo" => 1, "bar" => "baz"}
```

如果 `MyHash` 是泛型，那么上面的等价于：

```crystal
my_hash = MyHash(typeof("foo", "bar"), typeof(1, "baz")).new
my_hash["foo"] = 1
my_hash["bar"] = baz
```

可以在类型名中显式标注类型参数：

```crystal
MyHash(String, String | Int32) {"foo" => "bar"}
```
