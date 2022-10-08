# Crystal 编程语言

这是 Crystal 语言的参考文档。[目录](SUMMARY.md)

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

#### mdbook
鉴于gitbook 命令行版已经失去维护，我们可以用 [mdbook](https://rust-lang.github.io/mdBook) 进行预览。
```
$ mdbook serve
```
应该能看到
```
2022-10-07 22:06:15 [WARN] (mdbook::book): It appears you are still using book.json for configuration.
2022-10-07 22:06:15 [WARN] (mdbook::book): This format is no longer used, so you should migrate to the
2022-10-07 22:06:15 [WARN] (mdbook::book): book.toml format.
2022-10-07 22:06:15 [WARN] (mdbook::book): Check the user guide for migration information:
2022-10-07 22:06:15 [WARN] (mdbook::book):      https://rust-lang.github.io/mdBook/format/config.html 
2022-10-07 22:06:16 [INFO] (mdbook::book): Book building has started
2022-10-07 22:06:16 [INFO] (mdbook::book): Running the html backend
2022-10-07 22:06:28 [INFO] (mdbook::cmd::serve): Serving on: http://localhost:3000
2022-10-07 22:06:28 [INFO] (warp::server): Server::run; addr=[::1]:3000   
2022-10-07 22:06:28 [INFO] (warp::server): listening on http://[::1]:3000 
```
#### gitbook
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
或者通过docker方式：
```
$ docker-compose up
...
gitbook_1  | Starting server ...
gitbook_1  | Serving book on http://localhost:4000
gitbook_1  | Restart after change in file node_modules/.bin
...
```
