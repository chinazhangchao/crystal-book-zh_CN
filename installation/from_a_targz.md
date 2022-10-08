# 从 tar.gz

如果出于某些原因你不能或不想用之前的安装方法，你仍然可以下载一个单独的tar.gz，里面有运行Crystal所需的所有东西。

最新的文件可从Github的发布页下载： https://github.com/crystal-lang/crystal/releases

下载对应你平台的文件，解压它，你在里面可以找到 `bin/crystal` 可执行文件。

为了方便使用，你可以在PATH中创建一个符号链接：

`ln -s [full path to bin/crystal] /usr/local/bin/crystal`

这样你就可以用 `crystal`命令来简单地调用编译器。
