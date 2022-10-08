# 在 Windows 的 Ubuntu 子系统上使用 Bash

Crystal *目前* 还不支持 Windows 。但是如果你在用 Windows 10，你可以 (实验性的) [在 Windows 的 Ubuntu 子系统上使用 Bash](https://msdn.microsoft.com/en-us/commandline/wsl/about)尝试Crystal。既然它也是Bash环境，系统类似于[Debian/Ubuntu](on_debian_and_ubuntu.md)，那么安装的方法也类似，只是要注意一些地方。

别忘了，**这不过是个实验**。

## 建立仓库

首先你要在你的 APT 配置中加入仓库，简单起见你只要输入如下命令：

```
curl -sSL https://dist.crystal-lang.org/apt/setup.sh | sudo bash
```

这会把仓库和秘钥一起加入配置。如果你要手动做这些，请输如下命令：

```
sudo apt-key adv --keyserver keys.gnupg.net --recv-keys 09617FD37CC06B54
echo "deb https://dist.crystal-lang.org/apt crystal main" | sudo tee /etc/apt/sources.list.d/crystal.list
sudo apt-get update
```

## 依赖
Crystal 需要 C 编译器 (`cc`)和链接器 (`ld`) 来编译Crystal 程序——所以你应当安装它：

```
sudo apt-get install clang binutils
```

## 安装
一旦仓库建立完毕，所有依赖项已安装，你就可以安装 Crystal：

```
sudo apt-get install crystal
```

## 升级

当新的 Crystal 版本发布时，你可以这样升级：

```
sudo apt-get update
sudo apt-get install crystal
```
