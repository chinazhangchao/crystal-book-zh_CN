# 宏内变量(Fresh variables)

宏一旦展开，它就会像正常的Crystal代码一样被分析，里面的代码可能涉及原作用域中已定义的变量。

用例子更好理解：

```crystal
macro update_x
  x = 1
end

x = 0
update_x
x #=> 1
```

有时候这个很有用，你不必特地去读/写局部变量了，不过它也可能不小心覆盖局部变量。你可以用 `%name` 创建一个宏内变量来避免这个事故：

```crystal
macro dont_update_x
  %x = 1
  puts %x
end

x = 0
dont_update_x # outputs 1
x #=> 0
```

上例中，我们定义的 `%x` 在编译器保证不会覆盖到现有作用域的任何一个局部变量。

另外，涉及其他AST节点的宏内变量可以用 `%var{key1, key2, ..., keyN}`声明。例如：

```crystal
macro fresh_vars_sample(*names)
  # First declare vars
  {% for name, index in names %}
    print "声明: ", "%name{index}", '\n'
    %name{index} = {{index}}
  {% end %}

  # Then print them
  {% for name, index in names %}
    print "%name{index}: ", %name{index}, '\n'
  {% end %}
end

fresh_vars_sample a, b, c

# 样例输出:
# 声明: __temp_255
# 声明: __temp_256
# 声明: __temp_257
# __temp_255: 0
# __temp_256: 1
# __temp_257: 2
```

如上，三个下标变量被生成，求值，然后打印，表示它们对应的下标。
