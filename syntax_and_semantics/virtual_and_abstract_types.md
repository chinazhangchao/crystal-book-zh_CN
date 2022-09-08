# 虚类型和抽象类型

当变量的类型是同一等级下的多种类型的组合时，它的类型就是**虚类型**。这对于除了`Reference`，`Value`，`Int` 和 `Float`之外的所有类型均适用。例如：

```crystal
class Animal
end

class Dog < Animal
  def talk
    "Woof!"
  end
end

class Cat < Animal
  def talk
    "Miau"
  end
end

class Person
  getter pet

  def initialize(@name : String, @pet : Animal)
  end
end

john = Person.new "John", Dog.new
peter = Person.new "Peter", Cat.new
```

如果你加上 `tool hierarchy` 参数来编译该文档，你会发现，在 `Person`的部分：

```
- class Object
  |
  +- class Reference
     |
     +- class Person
            @name : String
            @pet : Animal+
```

你可以看到 `@pet` 类型是 `Animal+`。 其中 `+` 意味着它是一个虚类型，意思是“任何继承 `Animal` 的类型，包括`Animal`”。

编译器总会把同一等级下的联合类型处理成虚类型：

```
if some_condition
  pet = Dog.new
else
  pet = Cat.new
end

# pet : Animal+
```

同样地，编译器会尝试这些事情：如果多个类型在同一层类型体系下可以找到公共的超类(除了`Reference`，`Value`，`Int` 和 `Float`)，那么就把它的类型设为对应的虚类型。否则就保留联合类型。

编译器这么做的理由是为了免于创建多种相近的联合类型，更快地编译程序，同时使产生的代码减小。另一方面，这也有意义：在同一等级下的多个类应当有详尽的行为。

现在我们让 John 的宠物叫两声：

```crystal
john.pet.talk # 错误: Animal 没有定义方法 'talk' 
```

这个错误之所以产生，是因为编译器把`@pet`视为`Animal+`，因此它可能是 `Animal`。可`Animal`下面又没有定义`talk`方法。

然而我们比编译器多知道： `Animal` 永远不会被实例化，因为这一个实例不会有任何意义。我们要用`abstract`关键字告诉编译器这个类是抽象的： 

```crystal
abstract class Animal
end
```

现在代码可以通过编译:

```crystal
john.pet.talk #=> "Woof!"
```

把类标记为抽象类可以阻止人创建它的示例:

```crystal
Animal.new # 错误: 不能创建抽象类 Animal 的实例
```

如果我们要显式地说明 `Animal`必须定义有 `talk` 方法，我们可以把它作为抽象方法加进 `Animal` 里面：

```crystal
abstract class Animal
  # 要求动物能叫，即定义方法 talk
  abstract def talk
end
```

只要我们把方法标注为 `abstract` ，编译器会知道所有继承这个类的类型都要包含这个方法，即使程序没有用到它

抽象方法也可以定义于模块中，这样编译器会检查所有包含这个模块的类型，要求它们必须定义这个方法。
