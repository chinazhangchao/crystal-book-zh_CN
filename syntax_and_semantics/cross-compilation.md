# 交叉编译

Crystal 支持基础形式的 [交叉编译](http://en.wikipedia.org/wiki/Cross_compiler).

为了使用这个功能，编译器提供了两个标志：

* `--cross-compile`：启用交叉编译模式
* `--target`: 设置目标的 [LLVM 目标三元组](http://llvm.org/docs/LangRef.html#target-triple)，这会改变 [编译时标志](compile_time_flags.html)的默认设置

如果LLVM的版本在3.5及以上，你可以用 `llvm-config --host-target` 命令得到 `--target` 标志。对 linux，它可能是 "x86_64-unknown-linux-gnu"。

如果你需要设置`--target` 之外的标志，你可以用`-D`命令行标志进行设置。

用这两个参数，你可以在 Mac 上编译程序，然后在 Linux 上运行：

```bash
crystal build your_program.cr --cross-compile --target "x86_64-unknown-linux-gnu"
```

这会创建一个 `.o` ([对象文件](http://en.wikipedia.org/wiki/Object_file)) ，然后打出一个命令行，你应该在目标系统上执行它。例如：

```bash
cc your_program.o -o your_program -lpcre -lrt -lm -lgc -lunwind
```

你应当把这个 `.o` 文件复制到目标系统，然后执行这个命令。然后这个.o文件才会真正地被链接成为可执行文件。

这个过程通常由编译器自己完成，用于把编译器移植到一个尚没有编译器的系统。编译Crystal需要一个先前的Crystal编译器，但是如果没有编译器呢？那就只有两种办法：

* 检查用 Ruby 写成的最新版本的编译器，用它编译下一个版本，下下一个版本，直到当前的版本。
* 生成一个目标系统的 `.o` 文件，然后向目标系统链接它。

第一个过程又长又累人，第二个相比就比较简单了。

交叉编译也可以用于其他的程序，但主要的目标是编译器。如果有些系统不支持Crystal，你就可以自己编译一个编译器出来。
