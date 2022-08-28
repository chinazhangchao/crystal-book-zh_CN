# 字面量

Crystal 为创建基本类型的创建都提供了多种字面量。
 
| 字面量                                    | 示例                                         |
|---                                        |---                                          |
| [Nil](./literals/nil.html)                | `nil`                                       |
| [布尔值](./literals/bool.html)              | `true`, `false`                             |
| [整数](./literals/integers.html)      | `18`, `-12`, `19_i64`, `14_u32`,`64_u8`     |
| [浮点数](./literals/floats.html)          | `1.0`, `1.0_f32`, `1e10`, `-0.5`            |
| [字符](./literals/char.html)              | `'a'`, `'\n'`, `'あ'`                       |
| [字符串](./literals/string.html)          | `"foo\tbar"`, `%("あ")`, `%q(foo #{foo})`   |
| [符号](./literals/symbol.html)          | `:symbol`, `:"foo bar"`                     |
| [数组](./literals/array.html)            | `[1, 2, 3]`, `[1, 2, 3] of Int32`, `%w(one two three)` |
| [数组型变量](./literals/array.html#array-like-type-literal) | `Set{1, 2, 3}`                              |
| [散列](./literals/hash.html)              | `{"foo" => 2}`, `{} of String => Int32`     |
| [散列型变量](./literals/hash.html#hash-like-type-literal) | `MyType{"foo" => "bar"}`                    |
| [范围](./literals/range.html)            | `1..9`, `1...10`, `0..var`                  |
| [正则表达式](./literals/regex.html)            | `/(foo)?bar/`, `/foo #{foo}/imx`, `%r(foo/)` |
| [元组](./literals/tuple.html)            | `{1, "hello", 'x'}`                         |
| [命名元组](./literals/named_tuple.html) | `{name: "Crystal", year: 2011}`, `{"this is a key": 1}`|
| [闭包(Proc)](./literals/proc.html)              | `->(x : Int32, y : Int32) { x + y }`        |
