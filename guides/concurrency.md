# 并发

## 并发 vs. 并行

“并发”和“并行”的定义有时会混杂起来，但根本上来说它们不一样。

一个并发系统与可以做多个任务的系统，至于是否真的在同时执行尚在其次。你可以想象你在厨房做饭：你切洋葱，把它扔进锅里炸，炸的同时你又在切西红柿。如此种种时你没有同时做所有事情，你只是把时间分配到不同的任务上。至于并行，就是你一只手在炸洋葱，另一只手又在切西红柿。

此文写成之时， Crystal仍没有支持并行。只是支持并发。多个任务都能执行，每个都能分配到一些时间，但是两个任务绝不会同时执行。

> 译注: 1.5版本(2022年)的crystal文档中没有说明多线程并行，然而相关代码在2019年已经[合并](https://github.com/crystal-lang/crystal/pull/8112)到crystal的主分支

一个 Crystal 程序运行在一个操作系统线程上,只有"标记——清扫"型垃圾收集器是并行的(目前是 [Boehm GC](http://www.hboehm.info/gc/))。

### 纤程(Fibers)

为了实现并发， Crystal 添加了纤程 (fibers)。一个 纤程类似于操作系统线程,但是更加轻量，并且由进程内部管理。因此一个程序可以启动多个纤程，并且Crystal会保证在合适的时候执行它们。

### 事件循环

每个有关 I/O 的对象都配有一个事件循环。有些耗时的操作会被委派给它们, 当事件循环等待这个操作执行时，程序可以执行其他的纤程。一个简单的例子是等待从 socket中传来的数据。

### 管道(Channels)

Crystal有受[CSP](https://en.wikipedia.org/wiki/Communicating_sequential_processes)启发而得的管道 (Channels)。它们可以在纤程间传递信息而不用手动传递内存，因此编程时也不用担心锁，信号量等结构和问题。

> 译注: 原文不便访问，[这篇文档](https://zhuanlan.zhihu.com/p/455843256)讲了Actor和CSP两种并发模型。
## 一个程序的执行流程

当一个程序启动时，他会启动在你顶层代码执行的主纤程。一个纤程可以启动其他纤程。程序大概由这几部分组成：

* 运行时调度器：在合适的时间执行所有纤程
* 事件循环：它也是另一个纤程，用来管理异步任务，例如文件，socket，管道(pipes)，信号量和计时器(比如执行 `sleep`)。
* 管道：用以在纤程间传递数据。运行时调度器会协调纤程和管道之间的通讯。
* 垃圾收集器: 清理 "不再有用"的内存。

### 纤程fiber)

纤程(fiber)是程序执行的单元，但是比线程更轻量。它就是一个关联到一个of 8MB  [栈](https://en.wikipedia.org/wiki/Call_stack) 的小对象, 而栈则被分配给一个操作系统线程。

纤程是协作式的，这一点与线程不同。线程是抢占式的：操作系统可能在任何时候打断一个线程其执行另一个。一个纤程必须显式地通知调度器去执行其他纤程。举个例子，如果一个纤程要等待 I/O 操作， 它会告诉调度器： “看，我要等这个 I/O 操作可用，你先执行其他纤程，等这个 I/O 好的时候再叫我。”

协作式执行的好处是：这避免了切换上下文(切换纤程时所必须)的一大堆开销。

纤程比一线程轻量之处在于：即使它被分配了 8MB的栈空间，他一开始也只是一个 4KB的小栈。

在 64位机器上我们可以启动不计其数的纤程，而在32位机上我们最多启动 512个纤程。不过32位机现在已经濒临过时，我们以后就只注重64位机。

### 运行时调度器

调度区用一个队列容纳：
* 就绪的纤程：比如你启动一个纤程时，他就是就绪的，可以执行。
* 事件循环： 其实也是纤程. 当没有其他可执行的纤程时事件循环会检查有没有异步操作完成，如果有，就执行依赖于它的纤程。事件循环目前是由 `libevent`实现，它是`epoll` 或 `kqueue`等机制的抽象。
* 自发要求等待的纤程：这可以由 `Fiber.yield`指定，意思是 “我本可以继续执行，但是我给你机会去执行其他的纤程。”

### 数据交流

因为现在只有一个事实的线程在执行程序，多个纤程获取和更改同一个类变量暂时没有问题。但是一旦语言中实现了多线程 (并行) 它就可能有隐患。因此不要这样传递数据，而是建立管道，通过管道发送消息。在内部，一个管道实现了加锁机制以避免数据竞争，然而在外部，你把它当做交流的基本方式。这样你(用户)就不必手动加锁。

## 代码示例

### 生成一个纤程

用 `spawn` 加一个块来生成纤程：

```crystal
spawn do
  # ...
  socket.gets
  # ...
end

spawn do
  # ...
  sleep 5.seconds
  #  ...
end
```

现在我们有了两个纤程：一个从socket中读取消息，另一个只是做 `sleep`。当第一个纤程到达 `socket.gets` 一行，它就会被挂起，事件循环会记得在socket有消息后执行这个纤程。然后程序会执行第二个纤程。这个纤程想要睡5秒，因此事件循环会记得在5秒后执行这个纤程。如果此时没有其他纤程可执行，事件循环会等到这些事件发生，而不用占用CPU时间。

`socket.gets` 和 `sleep` 表现如此的原因是它们的实现直接面对运行时调度器，这不是什么玄学。只是标准库往往已经包揽了这些工作，你不用手动做而已。

然而注意，纤程不会在创建时立刻执行。例如：

```crystal
spawn do
  loop do
    puts "Hello!"
  end
end
```

运行这些代码，程序就会利己退出，什么输出也不会有。

这是因为纤程并不会在创建时立即执行。因此，主纤程创建了这个纤程，自己执行，然后整个程序就退了出。

一个解决方案是加 `sleep`：

```crystal
spawn do
  loop do
    puts "Hello!"
  end
end

sleep 1.second
```

这个程序会在一秒内持续打印打印 "Hello!"，然后退出。这是因为 `sleep`会让主纤程等到一秒后才被执行，这时程序会执行另一个就绪的纤程，即上面一直打 "Hello!"的那个。

另一种方法是：

```crystal
spawn do
  loop do
    puts "Hello!"
  end
end

Fiber.yield
```

这次 `Fiber.yield` 会告诉调度器去执行其他纤程。这会打印 "Hello!" 直到标准输出阻塞 (系统调用会告诉我们等到标准输出可用的时候)，然后主纤程恢复执行，程序退出。这里标准输出*也许* 不会阻塞，于是程序就会一直执行下去。

如果我们想要永远执行一个所创建的纤程，我们可以使用不加参数的 `sleep`：

> 译注 : 永眠 ()
```crystal
spawn do
  loop do
    puts "Hello!"
  end
end

sleep
```

当然这个程序根本用不着 `spawn`，直接写循环就行了。 `sleep` 在创建多个纤程时更有用。

### 生成方法调用

你也可以生成一个纤程去调用方法，而不是执行块。你可以看这个例子来明白它的用处：

```crystal
i = 0
while i < 10
  spawn do
    puts(i)
  end
  i += 1
end

Fiber.yield
```

上面的程序会打印 "10" 十次。问题在于所有创建的纤程都指向同一个变量 `i` ，当 `Fiber.yield`执行时，它的值是 10。

解决方法是：

```crystal
i = 0
while i < 10
  proc = ->(x : Int32) do
    spawn do
      puts(x)
    end
  end
  proc.call(i)
  i += 1
end

Fiber.yield
```

现在它正常的打印了。因为我们创建了 [闭包](http://crystal-lang.org/api/Proc.html) 来捕获 `i`，然后调用它，所以这个值被复制了，被创建的纤程得到的是原数的备份。

为了避免写样板代码，标准库提供了 `spawn` 宏。它接受一个调用表达式，然后把它重写成上面的样子。使用它，我们得到了：

```crystal
i = 0
while i < 10
  spawn puts(i)
  i += 1
end

Fiber.yield
```

这在局部变量碎迭代更改时非常有用。这不会用于块参数。例如，这个程序也能正常输出：

```crystal
10.times do |i|
  spawn do
    puts i
  end
end

Fiber.yield
```

### 生成一个纤程，等待它完成

为此我们可以使用管道：

```crystal
channel = Channel(Nil).new

spawn do
  puts "Before send"
  channel.send(nil)
  puts "After send"
end

puts "Before receive"
channel.receive
puts "After receive"
```

This prints:

```text
Before receive
Before send
After receive
```

首先程序创建了一个纤程，但不立刻执行它。当我们调用 `channel.receive`时，主纤程阻塞，这个被创建的纤程开始执行。然后 `channel.send(nil)` 被调用，继而 `channel.receive`被执行，他等待的值终于来了。然后主纤程继续执行，直到结束，随后程序退出，其他纤程来不及机会打印"After send"就被清理了。

上例中我们使用 `nil`只是为了告诉他人纤程的结束。我们也可以用管道在纤程之间传值：

```crystal
channel = Channel(Int32).new

spawn do
  puts "Before first send"
  channel.send(1)
  puts "Before second send"
  channel.send(2)
end

puts "Before first receive"
value = channel.receive
puts value # => 1

puts "Before second receive"
value = channel.receive
puts value # => 2
```

Output:

```text
Before first receive
Before first send
1
Before second receive
Before second send
2
```

注意当程序执行一个 `receive`时，纤程阻塞，另一个纤程执行。当执行 `send`时，控制流回到等待这个值的纤程。

这里我们在发送字面量，但是被创建的纤程可以用多种方式得到这个值。比如读一个文档，或是等待来自socket的消息。 当这个纤程必须等待 I/O时，其他的纤程可以趁这个机会执行，直到 I/O 完成。然后这个值终于通过管道传给了主纤程。例如：

```crystal
require "socket"

channel = Channel(String).new

spawn do
  server = TCPServer.new("0.0.0.0", 8080)
  socket = server.accept
  while line = socket.gets
    channel.send(line)
  end
end

spawn do
  while line = gets
    channel.send(line)
  end
end

3.times do
  puts channel.receive
end
```

上述程序创建了两个纤程。第一个创建了 TCPServer，等待一个连接，并且从中按行读取文本，把它们发给管道。第二个纤程从标准输入按行读取文本，主纤程从管道读取前三条消息息——而不管它从标准输入还是TCP服务器——然后程序退出 。 `gets`会阻塞纤程，告诉调度器有消息来了再启动它。

类似地我们可以等待多个纤程去完成执行，然后获取它们的值：

```crystal
channel = Channel(Int32).new

10.times do |i|
  spawn do
    channel.send(i * 2)
  end
end

sum = 0
10.times do
  sum += channel.receive
end
puts sum # => 90
```

当然，你可以在创建的纤程内使用 `receive`：

```crystal
channel = Channel(Int32).new

spawn do
  puts "Before send"
  channel.send(1)
  puts "After send"
end

spawn do
  puts "Before receive"
  puts channel.receive
  puts "After receive"
end

puts "Before yield"
Fiber.yield
puts "After yield"
```

输出:

```text
Before yield
Before send
Before receive
1
After receive
After send
After yield
```

此处 `channel.send` 先执行，但是因为目前没有纤程想要这个值，它就先挂起该纤程，等其他纤程执行。后来第二个纤程执行。它从管道中取值的时候，里面正好有一个值。于是它获得这个值，继续执行。接下来先执行第一个纤程，然后是主纤程，因为 `Fiber.yield` 把一个纤程放在执行队列的最后面。

### 缓存管道

上例中我们用的是无缓存的管道：当发送值时，如果一个纤程在等待这个值，那控制流就会转到那个纤程上。

通过使用带缓存的管道时，调用 `send` 不会利己跳到另外的纤程，除非缓存区已满：

```crystal
# 容量为 2 的缓存管道
channel = Channel(Int32).new(2)

spawn do
  puts "Before send 1"
  channel.send(1)
  puts "Before send 2"
  channel.send(2)
  puts "Before send 3"
  channel.send(3)
  puts "After send"
end

3.times do |i|
  puts channel.receive
end
```

输出：

```
Before send 1
Before send 2
Before send 3
1
2
After send
3
```

注意前两次发送执行时，控制流没有跑到其他纤程上，然而，在第三次发送时，管道的缓存已经满了，所以控制流回到了主纤程。主纤程收到两个值之后，管道又空了，所以第三次执行`receive`时，主纤程阻塞，控制流回到上面的纤程，它发送信息，然后结束。然后主纤程又去做接下来的事情。
