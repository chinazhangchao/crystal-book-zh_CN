# 宏方法(Macro methods)

宏方法可以帮助你在继承链中为每一个子类添加方法，而只用在基类中定义一次。

一个 `def` 如果含有涉及`@type`的宏展开式，那么就会隐式地成为 `macro def`。例如：

```crystal
class Object
  def instance_vars_names
    {{ @type.instance_vars.map &.name.stringify }}
  end
end

class Person
  def initialize(@name : String, @age : Int32)
  end
end

person = Person.new "John", 30
person.instance_vars_names #=> ["name", "age"]
```

宏定义中参数以AST节点的格式传递，你可以用展开式 (`{{ 宏参数 }}`)取得它。但是这一点对对宏方法不行。这里的参数列表属于宏方法所产生的方法，你不能在编译时期取得它的值。

```crystal
class Object
  def has_instance_var?(name) : Bool
    # 我们不能取得宏展开式里面的 name 
    # 而是通过宏展开创建一个array
    # 然后在运行时做包含检查
    {{ @type.instance_vars.map &.name.stringify }}.includes? name
  end
end

person = Person.new "John", 30
person.has_instance_var?("name") #=> true
person.has_instance_var?("birthday") #=> false
```
