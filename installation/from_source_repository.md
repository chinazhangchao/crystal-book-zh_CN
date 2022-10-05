# 从源码编译

如果你要参与改进编译器，你应该尝试从源码编译 Crystal。

1. [安装 Crystal 发布](https://crystal-lang.org/docs/installation)。 Crystal已经能实现自举，为了编译 Crystal，你需要一个现成的 Crystal :)。

2. 确保你的PATH中有受支持的 LLVM 版本。目前 Crystal 支持 LLVM 3.8，3.9，4.和5.0。尽量用最新的版本。如果你在用 Mac 和 Homebrew，这些会在你加入 `--with-llvm` 编译选项时自动配置。

3. 确保安装[所有依赖的库](https://github.com/crystal-lang/crystal/wiki/All-required-libraries)。你也应该了解 [语言改进指引](https://github.com/crystal-lang/crystal/blob/master/CONTRIBUTING.md).

4. 复制这个项目：

```
git clone https://github.com/crystal-lang/crystal
```

5. 运行 `make` 来编译你的自制版编译器。 
6. 运行 `make spec` 来确保所有的测试都通过，且你已经合适地安装了所有东西。
8. 用 `bin/crystal` 运行你的  crystal 文件。

如果你要深入地了解 `bin/crystal`，参考 [使用编译器](https://crystal-lang.org/docs/using_the_compiler/) 相关的文档。

注意：真正的二进制文件其实在 `.build/crystal`里，但是 `bin/crystal`是你真正应该调用的包装脚本。
