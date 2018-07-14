# Crystal 编程语言

这是 Crystal 语言的参考文档。

Crystal 语言的设计目标如下：

* 语法与Ruby类似（但兼容性不是目标）。
* 静态类型检查，但不需要显式指定变量或参数类型。
* 可在Crystal内通过代码绑定调用C代码。
* 编译期进行代码评估和生成，避免了样板式的代码。
* 可编译为高效的本地机器码。

## 伟大的开源力量

欢迎提交 pull request ：

https://github.com/chinazhangchao/crystal-book-zh_CN

一起来翻译吧^_^！QQ交流群号：823109001。

### 本地编译预览

```
$ git clone https://github.com/chinazhangchao/crystal-book-zh_CN.git
$ cd crystal-book
$ npm install -g gitbook-cli@2.3.0
$ gitbook install
$ gitbook serve
Live reload server started on port: 35729
Press CTRL+C to quit ...

info: 8 plugins are installed
info: loading plugin "ga"... OK
...
Starting server ...
Serving book on http://localhost:4000

```

```
$ docker-compose up
...
gitbook_1  | Starting server ...
gitbook_1  | Serving book on http://localhost:4000
gitbook_1  | Restart after change in file node_modules/.bin
...
```
