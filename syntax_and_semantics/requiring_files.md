# 导入文件

对于小的片段或是测试程序，把所有程序写到一个文件里尚可以接受。然而，大程序更适合分到多个文件里，以维持清晰的结构，增强可维护性。

要让编译器处理其他文档，你可以用 `require "..."`。 它接受一个单独的字符串字面量。`require "..."`有很多功能，下面将一一说明。

一旦文件被包含，编译器就会记住文件的绝对路径，对其重复的`require`会被忽略。

## require "filename"

查找包含目录里面的文件 "filename" 。

默认的包含目录是编译器指定的标准库目录，以及当前目录下的"lib" 子目录(由unix shell 的`pwd` 指定)。默认情况下只会查找这两个地方。

查找遵循如下规则：

* 如果包含目录中有文件叫 "filename.cr"， 那就导入它。
* 如果有目录叫 "filename" ，并且里面有文件 "filename.cr"直属于它，那就导入这个文件。
* 否则产生编译错误。

第二个规则非常方便，因为项目的典型目录结构是这样的：

```
- project
  - lib
    - foo
      foo.cr
    - bar
      bar.cr
  - src
    - project.cr
  - spec
    - project_spec.cr
```

比如，如果你在`project.cr`中写`require "foo"`以导入foo，然后在项目根目录中运行  `crystal src/project.cr`，它就在 `lib/foo/foo.cr`中找到 `foo`。

如果你从其他地方运行编译器,比如说 `src`文件夹，那么 `lib`就不在路径中， `require "foo"`也不会被找到。

## require "./filename"

这会从文档的当前位置以相对路径查找 "filename"。

查找遵循如下规律：

* 如果有文件 "filename.cr" 在当前文档中，那就包含它。
* 如果有目录 "filename" ，而且里面有文件 "filename.cr"直属于它，那就导入这个文件。
* 否则产生编译错误。

这个关系往往用于引用项目内的其他文件。它也用于从[specs](../guides/testing.md)中引用项目的文件：

```crystal
# 在 spec/project_spec.cr
require "../src/project"
```

## 其他形式

这两种形式都支持嵌套名称，这会导到对应的嵌套文件或目录里面：

* `require "foo/bar/baz"` 会在包含目录中查找 "foo/bar/baz.cr" 或 "foo/bar/baz/baz.cr" 
* `require "./foo/bar/baz"` 会从当前目录以相对路径查找 "foo/bar/baz.cr" 或 "foo/bar/baz/baz.cr"

你也可以用 "../" 去访问当前文件的上级目录，所以 `require "../../foo/bar"`也可以使用。

这两种形式都支持 `*` 和 `**` 后缀：

* `require "foo/*"` 会导入"foo"文件夹中所有的 ".cr"文件，但到这层为止，不会递归地包含底下的目录。
* `require "foo/**"` 会导入"foo"文件夹中所有的 ".cr"文件，同时会递归地包含底下的目录。
