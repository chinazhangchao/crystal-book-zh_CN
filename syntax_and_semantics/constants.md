# 常量

常量可以在顶层或是其他类型内定义，它们必须以大写字母开头：

```crystal
PI = 3.14

module Earth
  RADIUS = 6_371_000
end

PI #=> 3.14
Earth::RADIUS #=> 6_371_000
```

虽然编译器不强制，但常量往往以全大写 + 下划线构成。

常量定义中可以调用方法，也可以有复杂的逻辑：

```crystal
TEN = begin
  a = 0
  while a < 10
    a += 1
  end
  a
end

TEN #=> 10
```

# 伪常量

Crystal 提供了一些伪常量，以反射源代码中的信息：

`__LINE__` 是当前执行中的crystal 文件中的当前行号。当 `__LINE__` 被声明为方法的默认值时，它表示方法调用时的行号。

`__END_LINE__` 表示当前块的 `end` 所在的行号。它只能用于方法的默认值。

`__FILE__` 当前所执行文档的完整路径。

`__DIR__` 表示当前执行中的文件所在目录的完整路径。

```crystal
# 假设这些代码保存于: /crystal_code/pseudo_constants.cr
#
def pseudo_constants(caller_line = __LINE__, end_of_caller = __END_LINE__)
  puts "Called from line number: #{caller_line}"
  puts "Currently at line number: #{__LINE__}"
  puts "End of caller block is at: #{end_of_caller}"
  puts "File path is: #{__FILE__}"
  puts "Directory file is in: #{__DIR__}"
end


begin
  pseudo_constants
end

# 程序输出:
# Called from line number: 13
# Currently at line number: 5
# End of caller block is at: 14
# File path is: /crystal_code/pseudo_constants.cr
# Directory file is in: /crystal_code
```
