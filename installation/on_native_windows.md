# 在原生windows环境

> 译注：这是我自己添加的章节，不在官方内容之列。

在Windows原生环境编译Crystal [至今还在实验之中](https://github.com/crystal-lang/crystal/wiki/Porting-to-Windows)。

你可以从[发布页面](https://github.com/crystal-lang/crystal/releases)上下载Windows版对应的二进制包，但并非每个版本都会给Windows 提供包。截至2022年10月6日，最新的 Windows 包版本是1.5.0。

## 问题

`shards` 在拉取依赖包的时候，会在`./lib`路径创建符号链接，而这在Windows中是需要管理员权限的。所以运行`shards install`相关的命令时，务必以管理员身份执行命令。
