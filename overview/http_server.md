# HTTP Server

一个稍微有点意思的例子是 HTTP服务器:

```crystal
require "http/server"

server = HTTP::Server.new do |context|
  context.response.content_type = "text/plain"
  context.response.print "Hello world! The time is #{Time.now}"
end

address = server.bind_tcp 8080
puts "Listening on http://#{address}"
server.listen
```

上述代码等读完整个语言参考之后才会完全清楚，但是我们已经能学到一些东西了。

* 可以从其他文件中 [require](../syntax_and_semantics/requiring_files.html) 代码：

    ```crystal
    require "http/server"
    ```
* 可以定义 [局部变量](../syntax_and_semantics/local_variables.html) 而不需要指定类型：

    ```crystal
    server = HTTP::Server.new ...
    ```
* 用对象 HTTP::Server 的方法 bind_tcp 来指定 HTTP 服务器的端口 (这里端口设定为 8080)。
    ```crystal
    address = server.bind_tcp 8080
    ```
   

* 程序通过向对象调用 [方法](../syntax_and_semantics/classes_and_methods.html) (或发送消息) 来运行。

    ```crystal
    HTTP::Server.new ...
    ...
    Time.now
    ...
    address = server.bind_tcp 8080
    ...
    puts "Listening on http://#{address}"
    ...
    server.listen
    ```

* 可以使用代码块，或简称 [块](../syntax_and_semantics/blocks_and_procs.html)。这可以方便地重用代码，也可以获得函数式编程的部分优点：

    ```crystal
    HTTP::Server.new do |context|
      ...
    end
    ```

* 可以通过向字符串嵌入内容来构造它，这称为字符串插值。语言也为创建数组，散列，范围和元组等对象都提供了方便的[语法](../syntax_and_semantics/literals.html)：

    ```crystal
    "Hello world! The time is #{Time.now}"
    ``` 

