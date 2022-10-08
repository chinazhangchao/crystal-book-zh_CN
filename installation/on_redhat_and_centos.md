# 在 RedHat 和 CentOS

在 RedHat系分发版上，你可以用官方的 Crystal 仓库。

## 建立仓库
首先你要在你的 YUM 配置中加入仓库，简单起见你只要输入如下命令：

```
curl https://dist.crystal-lang.org/rpm/setup.sh | sudo bash
```
这会把仓库和秘钥一起加入配置。如果你要手动做这些，请输入如下命令：

```
rpm --import https://dist.crystal-lang.org/rpm/RPM-GPG-KEY

cat > /etc/yum.repos.d/crystal.repo <<END
[crystal]
name = Crystal
baseurl = https://dist.crystal-lang.org/rpm/
END
```

## 安装

一旦仓库建立完毕，你就可以安装 Crystal：

```
sudo yum install crystal
```

## 升级

当新的 Crystal 版本发布时，你可以这样升级：

```
sudo yum update crystal
```
