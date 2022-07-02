# 正则表达式(Regular Expressions)

正则表达式以 [Regex](http://crystal-lang.org/api/Regex.html) 类表示。

正则表达式往往由正则字面量遵循 [PCRE](http://pcre.org/pcre.txt)语法构建。它由用正斜杠(`/`)包含的 UTF-8字符构成：

```crystal
/foo|bar/
/h(e+)llo/
/\d+/
/あ/
/啊/
```

## 转义

正则表达式大转义遵循[字符串字面量的转义](./string.html).

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
'\uNNNN' # 十六进制表示的Unicode字符
'\u{NNNN...}' # 十六进制表示的Unicode字符
```

分隔符 `/` 在用正斜杠包裹的字符串中也必须转义。
注意：如果PCRE中的特殊字符想要以字面方式使用，那也需要转义。

## 插值

正则表达式字面量中的转义如同 [字符串字面量](./string.html). 注意如果插值得到的字符串不能构成一个正则表达式，那么这会导致运行期异常。

## 模式设定
尾部分隔符可以加后缀来改变正则表达式的匹配模式。

* `i`: 忽略大小写 (`PCRE_CASELESS`):  Unicode 字母既可以匹配目标中的大写字母，也可以匹配小写字母。
* `m`: 多行匹配 (`PCRE_MULTILINE`): *行首* (`^`) 和 *行尾* (`$`) 匹配字符串行之间空格的刚开始，或是结束前。
* `x`: 扩展空白符号匹配(extended whitespace matching) (`PCRE_EXTENDED`): 模式里的大多数空白字符都被忽略，除非在另外一个字符类中。不被转义的井号`#`表示从它开始的一行注释，直到行尾。
> 译注: 此处原文为 : Most white space characters in the pattern are totally ignored except when ignore or inside a character class. Unescaped hash characters `#` denote the start of a comment ranging to the end of the line.

```crystal
/foo/i.match("FOO")         # => #<Regex::MatchData "FOO">
/foo/m.match("bar\nfoo")    # => #<Regex::MatchData "foo">
/foo /x.match("foo")        # => #<Regex::MatchData "foo">
/foo /imx.match("bar\nFOO") # => #<Regex::MatchData "FOO">
```

## 百分号正则字面量

除了斜杠字面量,正则表达式还可以以 `%r` 接一对分隔符的方式构建。可用的分隔符是圆括号 `()`，方括号 `[]`，花括号 `{}`，尖括号 `<>`和竖杠 `||`. 除了竖杠，其他的分隔符都可以嵌套。

它们对于写包含正斜杠的正则表达式很有用。

```crystal
%r((/)) # => /(\/)/
%r[[/]] # => /[\/]/
%r{{/}} # => /{\/}/
%r<</>> # => /<\/>/
%r|/|   # => /\//
```
