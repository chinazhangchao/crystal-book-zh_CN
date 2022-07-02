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

Besides double-quotes strings, Crystal also supports string literals indicated by a percent sign (`%`) and a pair of delimiters. Valid delimiters are parenthesis `()`, square brackets `[]`, curly braces `{}`, angles `<>` and pipes `||`. Except for the pipes, all delimiters can be nested meaning a start delimiter inside the string escapes the next end delimiter.

These are handy to write strings that include double quotes which would have to be escaped in double-quoted strings.

```crystal
%(hello ("world")) # => "hello (\"world\")"
%[hello ["world"]] # => "hello [\"world\"]"
%{hello {"world"}} # => "hello {\"world\"}"
%<hello <"world">> # => "hello <\"world\">"
%|hello "world"|   # => "hello \"world\""
```

A literal denoted by `%q` does not apply interpolation nor escapes while `%Q` has the same meaning as `%`.

```crystal
name = "world"
%q(hello \n #{name}) # => "hello \\n \#{name}"
%Q(hello \n #{name}) # => "hello \n world"
```

## Multiline strings

Any string literal can span multiple lines:

```crystal
"hello
      world" # => "hello\n      world"
```

Note that in the above example trailing and leading spaces, as well as newlines,
end up in the resulting string. To avoid this a string can be split into multiple lines
by joining multiple literals with a backslash:

```crystal
"hello " \
"world, " \
"no newlines" # same as "hello world, no newlines"
```

Alternatively, a backslash followed by a newline can be inserted inside the string literal:

```crystal
"hello \
     world, \
     no newlines" # same as "hello world, no newlines"
```

In this case, leading whitespace is not included in the resulting string.

## Heredoc

A *here document* or *heredoc* can be useful for writing strings spanning over multiple lines.
A heredoc is denoted by `<<-` followed by an heredoc identifier which is an alphanumeric sequence starting with a letter (and may include underscores). The heredoc starts in the following line and ends with the next line that starts with the heredoc identifier (ignoring leading whitespace) and is either followed by a newline or a non-alphanumeric character.

```crystal
<<-XML
<parent>
  <child />
</parent>
XML
```

Leading whitespace is removed from the heredoc contents according to the number of whitespace in the last line before the heredoc identifier.

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

It is possible to directly call methods on heredoc string literals, or use them inside parentheses:

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

A heredoc generally allows interpolation and escapes.

To denote a heredoc without interpolation or escapes, the opening heredoc identifier is enclosed in single quotes:

```crystal
<<-'HERE'
  hello \n #{world}
  HERE # => "hello \n #{world}"
```
