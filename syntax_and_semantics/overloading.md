# 重载

我们可以定义一个 `become_older` 方法，它接受一个数字，表示成长的年龄：

```crystal
class Person
  getter :age
    
  def initialize(@name : String, @age : Int = 0)
  end

  def become_older
    @age += 1
  end

  def become_older(years)
    @age += years
  end
end

john = Person.new "John"
john.age #=> 0

john.become_older
john.age #=> 1

john.become_older 5
john.age #=> 6
```

如果你定义名称相同，参数个数不同的多种方法，他们那会被认为是不同的方法。这被称为*方法重载*。

方法重载有多个指标：

* 参数的个数
* 加给参数的类型限制
* 参数需要的名称
* 方法是否接收 [块](blocks_and_procs.html)

例如，我们可以定义四种不同的 `become_older` 方法：

```crystal
class Person
  @age = 0

  # 年龄增长一岁
  def become_older
    @age += 1
  end

  # 按指定年龄增长
  def become_older(years : Int32)
    @age += years
  end

  # 按字符串指定的年龄增长
  def become_older(years : String)
    @age += years.to_i
  end

  # 产出(yield)这个人当前的年龄,并且按块的返回值增加它
  def become_older
    @age += yield @age
  end
end

person = Person.new "John"

person.become_older
person.age #=> 1

person.become_older 5
person.age #=> 6

person.become_older "12"
person.age #=> 18

person.become_older do |current_age|
  current_age < 20 ? 10 : 30
end
person.age #=> 28
```

注意在那个有yield的方法的情况中，编译器发现方法里有一个`yield`表达式，所以知道它接受一个块。如果要明显地指出，你可以在参数列表末尾加入一个`&block` 参数。

```crystal
class Person
  @age = 0

  def become_older(&block)
    @age += yield @age
  end
end
```

在产生的文档中，这个 `&block` 方法将总是写出，不论你有没有显式地写出它。

参数个数相同时，编译器会尝试找到更*精确匹配*的方法定义，然后调用它。

```crystal
class Person
  @age = 0

  # 首先这个方法被定义了
  def become_older(age)
    @age += age
  end

  # 因为 "String" 比毫无限制要严格，编译器进行重载决议时，会让他优先于之前的那个。
  def become_older(age : String)
    @age += age.to_i
  end
end

person = Person.new "John"

# 调用第一个定义
person.become_older 20

# 调用第二个定义
person.become_older "12"
```

然而这个比较不是全序的(译注：如果有多个匹配，它们在不同的方面做了限制，编译器将犯难最终要用哪一个)。所以最好总是把最宽泛的方法放在最后面。
