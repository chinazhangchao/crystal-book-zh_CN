# sizeof

表达式`sizeof`返回一个 `Int32`,表示该类型变量以字节计占空间的大小。例如:

```crystal
sizeof(Int32)  #=> 4
sizeof(Int64)  #=> 8
```

对于引用([Reference](http://crystal-lang.org/api/Reference.html) )类型，这个大小等同于指向它的指针的大小 ：

```crystal
# 在 64 位机上
sizeof(Pointer(Int32)) #=> 8
sizeof(String)         #=> 8
```

这是因为引用类型变量的内存是在堆上分配的，传递时只传递它的指针。为了得到一个类的真正大小，请用 [instance_sizeof](instance_sizeof.html)。

sizeof 的参数是一个 [类型](type_grammar.html) ，往往与 [typeof](typeof.html)配合使用：

```crystal
a = 1
sizeof(typeof(a)) #=> 4
```
