# 在 Linux 上用 Linuxbrew

你可以用 [Linuxbrew](http://linuxbrew.sh/)来在某个Linux分发版上轻易地安装Crystal。

```
brew update
brew install crystal-lang
```

如果你要改进编译器，你也会需要安装LLVM。为此你可以被最后一行换成：

```
brew install crystal-lang --with-llvm
```
