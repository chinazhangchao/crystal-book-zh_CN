# 类方法

类方法是类和模块本身所拥有的方法，而不是示例所拥有的。

```crystal
module CaesarCipher
  def self.encrypt(string : String)
    string.chars.map{ |char| ((char.upcase.ord - 52) % 26 + 65).chr }.join
  end
end

CaesarCipher.encrypt("HELLO") # => "URYYB"
```

类方法可以以`类型名.方法名`的形式定义。

```crystal
def CaesarCipher.decrypt(string : String)
  encrypt(string)
end
```

当在模块和类内部定义类方法时用 `self`代替类型名更加方便。

类方法可以由 [extend 一个模块](modules.md#extend-self)的方式定义。

类方法可以由其定义时所用的名字调用 (`CaesarCipher.decrypt("HELLO")`)。当在同一个类和模块域调用时，接收者可以是 `self` 或者隐含的`self` (像 `encrypt(string)`)。

# 构造函数

构造函数也是类方法，但是拥有 [创建类的新实例](new,_initialize_and_allocate.md)的功能。

Crystal中的所有类型一般至少有一个叫做 `new`的构造函数，但他们也可以定义不同名字的构造函数。
