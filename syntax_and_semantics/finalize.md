# finalize

如果一个类定义了 `finalize`方法，那么它会在该类型的一个实例被回收的时候调用：

```crystal
class Foo
  def finalize
    # 当 Foo 被垃圾回收时调用
    # 用于释放未托管的资源 (比如 C 动态库或结构体)
  end
end
```

用这个方法释放没有被Crystal垃圾回收器直接管理的外部资源。

用例可见于 [`IO::FileDescriptor#finalize`](https://crystal-lang.org/api/IO/FileDescriptor.html#finalize-instance-method)
或 [`OpenSSL::Digest#finalize`](https://crystal-lang.org/api/OpenSSL/Digest.html#finalize-instance-method)。

**注意**：

- 只有被完整初始化的对象会调用`finalize` 方法。如果`initialize`的内部有异常抛出，`finalize`就不会被调用。如果你的类型定义了 `finalize`方法，记得揽住 `initialize`中任何可能出现的异常，并在处理异常时释放资源。

- 在垃圾回收期间创建新对象是未定义行为，这往往会导致程序整体崩溃。
