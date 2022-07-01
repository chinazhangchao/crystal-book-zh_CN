# 字符(Char)

[字符](http://crystal-lang.org/api/Char.html) 表示32位[Unicode](http://en.wikipedia.org/wiki/Unicode) [码点(code point)](http://en.wikipedia.org/wiki/Code_point).

它通常由单引号包住一个UTF-8字符字面量制成.

```crystal
'a'
'z'
'0'
'_'
'啊'
'あ'
```

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
'\uNNNN' # 十六进制表示的Unicode字符
'\u{NNNN...}' # 十六进制表示的Unicode字符
```

反斜杠接 `u` 表示一个Unicode码点。它既可以接正好四个十六进制字符(`\u0000` to `\uFFFF`)，也可以接由花括号标注的1-6个字符十六进制字符(`\u{0}` to `\u{10FFFF}`)。 

```crystal
'\u0041' # => 'A'
'\u{41}' # => 'A'
'\u{1F52E}' # => '&#x1F52E;'
```
