# 编译时标记

类型，方法，或是其他任何代码都可以依赖编译时标记给出的信息。这些标志一般从宿主的 [LLVM 目标三元组](http://llvm.org/docs/LangRef.html#target-triple)按 `-`分割而来。为了得到这些信息，你可以执行 `llvm-config --host-target`.

```bash
$ llvm-config --host-target
x86_64-unknown-linux-gnu

# 所以这些标志是: x86_64, unknown, linux, gnu
```

为了定义一个标志，只需要用 `--define` 或 `-D` 选项，例如：

```bash
$ crystal some_program.cr -Dflag
```

另外，如果程序以 `--release`编译，那也会添加 `release`标志。

你可以用`flag?`查看一个标志是否被定义：

```crystal
{% if flag?(:x86_64) %}
  # 对 64 位平台特化的代码
{% else %}
  # 对非 64 位平台特化的代码
{% end %}
```

`flag?` 返回一个布尔值，所以你可以配合 `&&` 和 `||`使用：

```crystal
{% if flag?(:linux) && flag?(:x86_64) %}
  # 对 64 linux 特化的代码  
{% end %}
```

这些标志往往用于C绑定，以条件性地定义类型和函数。例如，著名的 `size_t`类型在 Crystal 中是这样定义的：

```crystal
lib C
  {% if flag?(:x86_64) %}
    alias SizeT = UInt64
  {% else %}
    alias SizeT = UInt32
  {% end %}
end
```
