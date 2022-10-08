# 在 Mac OSX 上用 Homebrew

为了在 Mac 上简单地安装 Crystalyou ，你可以用 [Homebrew](http://brew.sh/).

```
brew update
brew install crystal
```

如果你要改进编译器，你也会需要安装LLVM。为此你可以被最后一行换成：

```
brew install crystal --with-llvm
```

## 对 OSX 10.11 (El Capitan) 的一些补缺

如果你遇到这种错误：

```
ld: library not found for -levent
```

你应当重新安装命令行工具，然后选择基础的工具链：

```
$ xcode-select --install
$ xcode-select --switch /Library/Developer/CommandLineTools
```
