# 字符串(String)

[字符串](http://crystal-lang.org/api/String.html) 代表一列不可更改的UTF-8字符。

字符串通常以双引号(`"`)包括的字符串字面量构建：

```crystal
"hello world"
```

## 转义

反斜杠有特殊功能，它可以表示转义序列，也可以表示一个Unicode码点。

可用的转义序列有：
```crystal
'\'' # 单引号
'\\' # 反斜杠
'\a' # 警报
'\b' # 退格
'\e' # 退出键(escape)
'\f' # 换页(form feed)
'\n' # 新行
'\r' # 回车
'\t' # (水平)制表符
'\v' # 垂直制表符
"\NNN" # 八进制ASCII字符
"\xNN" # 十六进制ASCII字符
'\uNNNN' # 十六进制表示的Unicode字符
'\u{NNNN...}' # 十六进制表示的Unicode字符
```

反斜杠接其他任何字符都表示源字符。

反斜杠接最多三个个从 0 到 7 的数，表示一个八进制格式的码点：

```crystal
"\101" # => "A"
"\123" # => "S"
"\12"  # => "\n"
"\1"   # 只有一个码点为 1 的字符的字符串
```

反斜杠接 `u` 表示一个Unicode码点。它既可以接正好四个十六进制字符(`\u0000` to `\uFFFF`)，也可以接由花括号标注的1-6个字符十六进制字符(`\u{0}` to `\u{10FFFF}`)。 

```crystal
"\u0041"    # => "A"
"\u{41}"    # => "A"
"\u{1F52E}" # => "&#x1F52E;"
```

只有大括号可以包含多个Unicode字符。字符之间用空白字符分隔。

```crystal
"\u{48 45 4C 4C 4F}" # => "HELLO"
```

## 插值

字符串字面量可以使用插值表达式来在运行时展开。

```crystal
a = 1
b = 2
"sum: #{a} + #{b} = #{a + b}" # => "sum: 1 + 2 = 3"
```

字符串插值也可以用 [String#%](https://crystal-lang.org/api/master/String.html#%25(other)-instance-method)完成。

插值部分可以放任何表达式，但是为了可读性，表达式最好小一些。

可以转义 `#` 号以取消插值，或是使用没有插值的字符串，比如 `%q()`。

```crystal
"\#{a + b}"  # => "#{a + b}"
%q(#{a + b}) # => "#{a + b}"
```

插值是通过 创建[String::Builder](http://crystal-lang.org/api/String/Builder.html) 和对每个插入 `#{...}`中的表达式调用`Object#to_s(IO)`来完成的。表达式 `"sum: #{a} + #{b} = #{a + b}"` 等价于：

```crystal
String.build do |io|
  io << "sum: "
  io << a
  io << " + "
  io << b
  io << " = "
  io << a + b
end
```

# 百分号字符串字面量

除了双引号形式的字符串，Crystal也支持百分号 (`%`)接一对分隔符构成的字面量.可用的分隔符是圆括号`()`，方括号 `[]`，花括号`{}`，尖括号  `<>`和竖杠。除了竖杠，其他的分隔符都可以嵌套，在内部如同普通的字符。

这个在写包含双引号的字符串时很有用，你不用手动转义每个双引号了。

```crystal
%(hello ("world")) # => "hello (\"world\")"
%[hello ["world"]] # => "hello [\"world\"]"
%{hello {"world"}} # => "hello {\"world\"}"
%<hello <"world">> # => "hello <\"world\">"
%|hello "world"|   # => "hello \"world\""
```

 `%q` 开头的字面量不接受转义或插值，而 `%Q`的意思等同于 `%`.

```crystal
name = "world"
%q(hello \n #{name}) # => "hello \\n \#{name}"
%Q(hello \n #{name}) # => "hello \n world"
```

## 多行字符串

任何一个字面量都可以跨多行：

```crystal
"hello
      world" # => "hello\n      world"
```

注意，在上面的例子里面尾随或前置的空格，新行，也在结果的字符串里面。你可以设多个字符串，然后用反斜杠连接：

```crystal
"hello " \
"world, " \
"no newlines" # 等同于 "hello world, no newlines"
```

另外，一个反斜杠接新行可以在字符串里取消新行。

```crystal
"hello \
     world, \
     no newlines" # 等同于 "hello world, no newlines"
```

此时前置的空白符号不算在字符串中。

## 立即文档(Heredoc)

一个 *立即文档* (英文为*heredoc*) 用于创建涉及多行的字符串时很有用。立即文档以 `<<-` 一个标识符(包含字母,数字,下划线)起头,正文从下一行开始,到看到开头的那个标识符时结尾，忽略新行和其他的空白字符。

```crystal
<<-XML
<parent>
  <child />
</parent>
XML
```

空白字符会根据末尾标识符出现的位置进行剪裁，裁掉标识符之前的部分。

```crystal
<<-STRING
  Hello
    world
  STRING # => "Hello\n  world"

<<-STRING
    Hello
      world
  STRING # => "  Hello\n    world"
```

可以直接调用立即文档的方法(因为他也是一个字符串)，也可以在括号内使用它。

```crystal
<<-SOME
hello
SOME.upcase # => "HELLO"

def upcase(string)
  string.upcase
end

upcase(<<-SOME
  hello
  SOME) # => "HELLO"
```

立即文档通常允许插值和转义。

可以通过在开头对标识符加单引号来禁止转义：

```crystal
<<-'HERE'
  hello \n #{world}
  HERE # => "hello \n #{world}"
```
