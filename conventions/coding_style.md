# 代码风格

这是标准库的代码风格。你可以使用它，这样其他开发者也会熟悉你的项目。

## 命名

**类名**用大驼峰。例如：

```crystal
class ParseError < Exception
end

module HTTP
  class RequestHandler
  end
end

alias NumericValue = Float32 | Float64 | Int32 | Int64

lib LibYAML
end

struct TagDirective
end

enum Time::DayOfWeek
end
```

**方法名**用下划线连接。例如：

```crystal
class Person
  def first_name
  end

  def date_of_birth
  end

  def homepage_url
  end
end
```

变量名也用下划线式。例如：

```crystal
class Greeting
  @@default_greeting = "Hello world"

  def initialize(@custom_greeting = nil)
  end

  def print_greeting
    greeting = @custom_greeting || @@default_greeting
    puts greeting
  end
end
```

**常量**全大写。例如： 

```crystal
LUCKY_NUMBERS     = [3, 7, 11]
DOCUMENTATION_URL = "http://crystal-lang.org/docs"
```

### 缩写

类名里面：字母缩写 *全大写*。比如 `HTTP`或 `LibXML`。

方法名里面： 字母缩写 *全小写*。比如 `#from_json`， `#to_io`。

### Libs

`Lib` 就用 `Lib`做前缀。比如。`LibC`， `LibEvent2`。

### 路径和文件名

项目内：

- `/` 含有 readme, 构建选项， (比如 CI 编辑器设置)，和其他项目级文件。 (比如 修改日志 或 贡献指南).
- `src/` 含有项目源代码。
- `spec/` 含有 [项目测试](../guides/testing.md)，它可以用 `crystal spec`执行。
- `bin/` 含有可执行文件。

文件以其类或命名空间的名字一致，用*下划线*分隔。

比如， `HTTP::WebSocket` 定义于 `src/http/web_socket.cr`。

## 空格

用 **双空格** 缩进命名空间，方法，块或其他作用域内部的代码。例如：

```crystal
module Scorecard
  class Parser
    def parse(score_text)
      begin
        score_text.scan(SCORE_PATTERN) do |match|
          handle_match(match)
        end
      rescue err : ParseError
        # 处理错误 ...
      end
    end
  end
end
```

类内，用**一行**分隔方法定义，常量，和内部类定义。比如：

```crystal
module Money
  CURRENCIES = {
    "EUR" => 1.0,
    "ARS" => 10.55,
    "USD" => 1.12,
    "JPY" => 134.15,
  }

  class Amount
    getter :currency, :value

    def initialize(@currency, @value)
    end
  end

  class CurrencyConversion
    def initialize(@amount, @target_currency)
    end

    def amount
      # 完成转换 ...
    end
  end
end
```

