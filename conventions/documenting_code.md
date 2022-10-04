# 代码注释

Crystal 文档注释使用[Markdown](https://daringfireball.net/projects/markdown/)的子集。

* 文档应当放在类，模块，方法的正上方，中间不留空格。

```crystal
# 独角兽是一种 **传奇生物** (见 `Legendary`模块) 
# 古籍中记载它们额头长有大而尖的角。
class Unicorn
end

# 不好: 这没有附加到任何类之上。

class Legendary
end
```

* 方法的文档应当包含方法概述和方法细节两部分。概述只包含第一行，后者包含整个文档注释。推荐的写法是：

  1. 第一行解释方法的意图和功能。
  2. 后接它的细节和用处。

例如：

``````crystal
# 返回独角兽角的数量。
#
# ```
# Unicorn.new.horns # => 1
# ```
def horns
  @horns
end
``````

* 使用第三人称: `Returns the number of horns this unicorn has` instead of `Return the number of horns this unicorn has`。
> 译注： 汉语没有第三人称相关的变格法，不过我们仍应当用客观的视角描述对象。我们说`返回独角兽角的数量`就可以了。

* 参数应当为 *斜体* (用单星号 `*` 或下划线 `_`包围)：

```crystal
# 创建独角兽，用 *horns* 指定角的数量。
def initialize(@horns = 1)
  raise "Not a unicorn" if @horns != 1
end
```

* Crystal 代码块应当用三个反引号`` ``` `` 包括，或是用四空格缩进。

``````crystal
# ```
# unicorn = Unicorn.new
# unicorn.speak
# ```
``````

或

```crystal
#     unicorn = Unicorn.new
#     unicorn.speak
```

* 文本块必须用三反引号包括，后接"text"关键字。这可以用于展示程序输出。

``````crystal
# ```text
# "I'm a unicorn"
# ```
``````

* 用单反引号包含其他类型，可以自动引用到该类型。

```crystal
# `Legendary` 模块
```

* 用井号接方法名来自动链接到当前类型的方法，并用单反引号包括起来。如 `#horns` 或 `#index(char)`。

* 用 `OtherType#method(arg1, arg2)` 或 `OtherType#method`的写法来自动链接到其他类型的方法，同样地，用单反引号包括起来。

例如：

```crystal
# 用 `#horns`检查角的数量
# 用 `Unicorn#speak`显示独角兽会说什么。
```

* 用 `# =>`指示代码框中表达式的值。

```crystal
1 + 2             # => 3
Unicorn.new.speak # => "I'm a unicorn"
```

* 用 `ditto` 表示和上一个注释相同的内容。

```crystal
# ditto
def number_of_horns
  horns
end
```

* 用 `:nodoc:` 隐藏它的文档。私有和保护变量总是被隐藏。

```crystal
class Unicorn
  # :nodoc:
  class Helper
  end
end
```

### 给类，模块，方法做标志

给出一个有效的关键字， Crystal 就会自动的创建可视化标签，以帮助显示问题和特殊事件。

目前支持的标志有：

- BUG
- DEPRECATED
- FIXME
- NOTE
- OPTIMIZE
- TODO

标志关键字必须是它们所在行的第一个词，且必须为全大写。为增强可读性，后面可以接一个冒号。

``````crystal
# 让独角兽向 STDOUT 讲话
#
# NOTE: 虽然独角兽平时不说话，但这只是特殊的。
# TODO: 检查它是否在睡觉。如果它不能说话，就抛出异常。
# TODO: 造另外一个 `speak`方法，接受并打印一个字符串。
def speak
  puts "I'm a unicorn"
end

# 让独角兽对 STDOUT 讲话
#
# DEPRECATED: 请改用 `speak`
def talk
  puts "I'm a unicorn"
end
``````

### 使用 Crystal 的代码格式化器

Crystal 有内置的格式化器，它不只能格式化你自己的代码，还能格式化文档代码块中的代码样例。

这会在 `crystal tool format`被调用的时候自动执行。它默认会格式化当前文件夹里所有的 `.cr` 文档。

格式化单个文档：

```
$ crystal tool format file.cr
```

格式化某个目录下的 `.cr` 文档：

```
$ crystal tool format src/
```

用这个工具可以同一代码风格，也可以给Crystal自己的库函数补充文档。

这个格式化器相当快，所以一次格式化一整个项目也不费多少时间。

### 完整的例子

``````crystal
# 独角兽是一种 **传奇生物** (见 `Legendary`模块) 
# 古籍中记载它们额头长有大而尖的角。
#
# 创建一只独角兽:
#
# ```
# unicorn = Unicorn.new
# unicorn.speak
# ```
#
# 上式会产生:
#
# ```text
# "I'm a unicorn"
# ```
#
# 用 `#horns`检查它角的数量。
class Unicorn
  include Legendary

  # 创建独角兽，用 *horns*指定角的数量
  def initialize(@horns = 1)
    raise "Not a unicorn" if @horns != 1
  end

  # 返回独角兽角的数量
  #
  # ```
  # Unicorn.new.horns # => 1
  # ```
  def horns
    @horns
  end

  # ditto
  def number_of_horns
    horns
  end

  # 让独角兽对 STDOUT 说话
  def speak
    puts "I'm a unicorn"
  end

  # :nodoc:
  class Helper
  end
end
``````

### 创建文档

为了给某个项目创建文档，只需要调用 `crystal docs`。这会在 `docs`目录创建一个静态网页形式的文档，以 `docs/index.html`作为入口点。所有 `src`目录下的代码都会被考虑。
