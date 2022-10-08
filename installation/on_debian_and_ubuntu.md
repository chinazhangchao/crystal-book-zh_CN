# 在 Debian 和 Ubuntu

在 Debian 系分发版中，你可以使用官方的 Crystal 仓库。

## 引入仓库

首先你要在你的 APT 配置中加入仓库，简单起见你只要输入如下命令：

```
curl https://dist.crystal-lang.org/apt/setup.sh | sudo bash
```

这会把仓库和秘钥一起加入配置。如果你要手动做这些，请以*root*身份输如下命令：

```
apt-key adv --keyserver keys.gnupg.net --recv-keys 09617FD37CC06B54
echo "deb https://dist.crystal-lang.org/apt crystal main" > /etc/apt/sources.list.d/crystal.list
apt-get update
```

## 安装

一旦仓库建立完毕，你就可以安装 Crystal：

```
sudo apt-get install crystal
```

[你可能需要](https://github.com/crystal-lang/crystal/issues/4342) 安装`build-essential` 包以运行/编译 Crystal程序。你可以用这个命令安装：

```
sudo apt-get install build-essential
```


## Upgrade

When a new Crystal version is released you can upgrade your system using:

```
sudo apt-get update
sudo apt-get install crystal
```
