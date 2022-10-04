# 属性

一些类型和方法可以被标注属性。这些属性暂时是固定的，但是我们最终(也许)会添加用户自定义的属性。

## Link

指示编译器如何链接一个C库。这会在 [lib](c_bindings/lib.html)章解释。

## Extern

标明某个 Crystal 结构体可以在库定义中应用：

```crystal
@[Extern]
struct MyStruct
end

lib MyLib
  fun my_func(s : MyStruct) # OK (如果没有 Extern 属性，就会报错)
end
```

你也可以让结构体看起来像 C 的联合体(union) ,尽管这个真的不安全：

```crystal
# 一个结构体，来简单地在 Int32 码点和 Char 之间做转化。
@[Extern(union: true)]
struct Int32OrChar
  property int = 0
  property char = '\0'
end

s = Int32OrChar.new
s.char = 'A'
s.int # => 65

s.int = 66
s.char # => 'B'
```

## ThreadLocal

`@[ThreadLocal]`属性可以加给类变量和外部C变量。这会让他们变成线程局部变量。

```crystal
class DontUseThis
  # 每个线程分别有一份
  @[ThreadLocal]
  @@values = [] of Int32
end
```

线程局部变量在标准库中使用，用于实现运行时。此外它不应该被用到。

## Packed

标志一个 [C 结构体](c_bindings/struct.html) 是紧密的，这样结构体的域之间就不会被填充用于对齐的字节。这个当C结构体显式地要求紧密结构体时使用。 

## AlwaysInline

提示编译器总是内联该方法：

```crystal
@[AlwaysInline]
def foo
  1
end
```

## NoInline

指示编译器不要内联一个方法调用。这不适用于带有`yield`的方法,因为它们总是被内联。

```crystal
@[NoInline]
def foo
  1
end
```

## ReturnsTwice

标志一个方法或 [C库函数](c_bindings/fun.html) 返回两次。 C 的 `setjmp` 是这种函数的一个例子。

## Raises

标志一个方法或[C库函数](c_bindings/fun.html)可能扔出一个异常。这在 [C 回调函数](c_bindings/callbacks.html)一章有解释。

## CallConvention

指示 [C库函数](c_bindings/fun.html)的调用约定。例如：

```crystal
lib LibFoo
  @[CallConvention("X86_StdCall")]
  fun foo : Int32
end
```

目前可用的调用约定有：

* C (默认值)
* Fast
* Cold
* WebKit_JS
* AnyReg
* X86_StdCall
* X86_FastCall

它们在[这里](http://llvm.org/docs/LangRef.html#calling-conventions)有解释。

## Flags

标志一个[枚举](enum.html) 是 "位枚举"，这会改变一些方法的行为，如 `to_s`。
