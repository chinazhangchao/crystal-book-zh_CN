# 使用编译器

[安装](../installation/README.md)完编译器后就可以使用 `crystal` 命令。

在下面的章节中美元符号(`$`)表示命令行。

## 一次编译运行

通过`crystal`命令后面加文件名就可以编译运行程序。

```
$ crystal some_program.cr
```

Crystal程序文件以`.cr`扩展名结尾。

也可以使用`run`命令：

```
$ crystal run some_program.cr
```

## 生成动态链接的可执行文件

可以使用`build`命令生成可执行文件：

```
$ crystal build some_program.cr
```

上面的命令会生成名为`some_program`的可执行文件：

```
$ ./some_program
```

**注意：** 默认生成的可执行文件**没有完全优化**。 打开优化开关，使用`--release`选项：

```
$ crystal build some_program.cr --release
```

一定要确保在生产环境和性能测试时总是使用`--release`。

没有完全优化时的性能也不错，编译时间也很短，因此你可以总是使用`crystal`命令，就好像在运行解释器一样。

可以通过`--no-debug`选项和`strip`命令减少二进制文件的大小。调试符号会被移除，只有当大小是主要问题并且不需要调试时才使用这个选项。

## 生成单独的可执行文件

生成单独的可执行文件，使用如下命令：

```
$ crystal build some_program.cr --release --static
```

更多有关静态链接的信息[查看这里](https://github.com/crystal-lang/crystal/wiki/Static-Linking)

## 创建项目或库

使用`init`命令创建带有标准目录结构的Crystal项目。

```
$ crystal init lib my_cool_lib
      create  my_cool_lib/.gitignore
      create  my_cool_lib/.editorconfig
      create  my_cool_lib/LICENSE
      create  my_cool_lib/README.md
      create  my_cool_lib/.travis.yml
      create  my_cool_lib/shard.yml
      create  my_cool_lib/src/my_cool_lib.cr
      create  my_cool_lib/src/my_cool_lib/version.cr
      create  my_cool_lib/spec/spec_helper.cr
      create  my_cool_lib/spec/my_cool_lib_spec.cr
Initialized empty Git repository in ~/my_cool_lib/.git/
```

## 其他命令和选项

要查看所有可用命令，直接输入`crystal`。

```
$ crystal
Usage: crystal [command] [switches] [program file] [--] [arguments]

Command:
    init                     generate a new project
    build                    build an executable
    deps                     install project dependencies
    docs                     generate documentation
    env                      print Crystal environment information
    eval                     eval code from args or standard input
    play                     starts crystal playground server
    run (default)            build and run program
    spec                     build and run specs (in spec directory)
    tool                     run a tool
    help, --help, -h         show this help
    version, --version, -v   show version
```

查看特定命令的选项，在命令后面使用`--help`：

```
$ crystal build --help
Usage: crystal build [options] [programfile] [--] [arguments]

Options:
    --cross-compile                  cross-compile
    -d, --debug                      Add full symbolic debug info
    --no-debug                       Skip any symbolic debug info
    -D FLAG, --define FLAG           Define a compile-time flag
    --emit [asm|llvm-bc|llvm-ir|obj] Comma separated list of types of output for the compiler to emit
    -f text|json, --format text|json Output format text (default) or json
    --error-trace                    Show full error trace
    -h, --help                       Show this message
    --ll                             Dump ll to Crystal's cache directory
    --link-flags FLAGS               Additional flags to pass to the linker
    --mcpu CPU                       Target specific cpu type
    --mattr CPU                      Target specific features
    --no-color                       Disable colored output
    --no-codegen                     Don't do code generation
    -o                               Output filename
    --prelude                        Use given file as prelude
    --release                        Compile in release mode
    -s, --stats                      Enable statistics output
    -p, --progress                   Enable progress output
    -t, --time                       Enable execution time output
    --single-module                  Generate a single LLVM module
    --threads                        Maximum number of threads to use
    --target TRIPLE                  Target triple
    --verbose                        Display executed commands
    --static                         Link statically
    --stdin-filename                 Source file name to be read from STDIN
```
