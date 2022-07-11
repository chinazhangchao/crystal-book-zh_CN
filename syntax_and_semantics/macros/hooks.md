# 钩子(Hooks)

在编译期，一些特殊的宏会作为钩子，在特定的时期调用：
* `inherited` 当子类定义时调用。 `@type` 是继承它的类型。
* `included` 当模块被包含时调用。 `@type` 是包含它的类型。
* `extended` 当模块被扩展时调用 `@type` 是扩展它的类型。
* `method_missing` 当方法没有找到时调用。
* `method_added` 当当前域有新方法添加时调用。

`inherited`的例子

```crystal
class Parent
  macro inherited
    def lineage
      "{{@type.name.id}} < Parent"
    end
  end
end

class Child < Parent
end

Child.new.lineage #=> "Child < Parent"
```

`method_missing`的例子：

```crystal
macro method_missing(call)
  print "尝试调用 ", {{call.name.id.stringify}}, " 用 ", {{call.args.size}}, " 个参数", '\n'
end

foo          # 输出: 尝试调用 foo 用 0 个参数
bar 'a', 'b' # 输出: 尝试调用 bar 用 2 个参数
```

`method_added`的例子：

```crystal
macro method_added(method)
  {% puts "方法添加:", method.name.stringify %}
end

def generate_random_number
  4
end
#=> 方法添加: generate_random_number 
```

`method_missing` 和 `method_added` 都只在宏被定义的域内调用。或者，如果宏在顶层定义，那就在顶层调用。例如：

```crystal
macro method_missing(call)
  puts "在外部域, 得到调用: ", {{ call.name.stringify }}
end

class SomeClass
  macro method_missing(call)
    puts "在某类内, 尝试调用: ", {{ call.name.stringify }}
  end
end

class OtherClass
end

# 这次调用被外部的`method_missing`处理
foo #=> 在外部域, 得到调用: foo

obj = SomeClass.new
# 这个由在类内的`method_missing`处理
obj.bar #=> 在某类内, 尝试调用: bar

other = OtherClass.new
# 不论是OtherClass还是它的父类都没有定义 `method_missing`
other.baz #=> 错误: 未定义的方法 'baz' 于 OtherClass 中
```

`finished` 在类型被完全定义时调用------即是类型的每一处扩展都完成的时候。看如下程序：

```crystal
macro print_methods
  {% puts @type.methods.map &.name %}
end

class Foo
  macro finished
    {% puts @type.methods.map &.name %}
  end
  print_methods
end

class Foo
  def bar
    puts "我是一个方法！"
  end
end

Foo.new.bar
```

`print_methods` 宏在遇到的时候就调用——然后打出一个空列表，因为这时候`Foo`里面还没有什么方法。当  `Foo` 的第二次也是最后一次被定义时， `finished` 宏会被调用，然后打出 `[bar]`。
