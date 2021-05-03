[TOC]
## 协程(Goroutine)
### Go 协程是什么?
`Go 协程`是与其他函数或方法`一起并发`运行的`函数或方法`。Go 协程可以看作是`轻量级线程`。与线程相比，创建一个 Go 协程的成本很小。因此在 Go 应用中，常常会看到有数以千计的 Go 协程并发地运行。

`并行是可以同时做很多事情，而并发是同时管理很多事情`，因为操作系统和硬件的总资源比较少，所以并发的效果要比并行好的多，使用较少的资源做更多的事情，这也是Go所提倡的。

### 如何启动一个协程?
`调用函数或者方法`时，在前面加上关键字 `go`，可以让一个新的 Go 协程并发地运行。
```go
package main

import (
   "fmt"
)

func hello() {
   fmt.Println("Hello world goroutine")
}
func main() {
   go hello()
   fmt.Println("main function")
}
```
在第11行，`go hello()` 启动了一个新的 Go 协程。现在 `hello()` 函数与 `main()` 函数会`并发`地执行。main函数会运行在一个特有的 Go 协程上，它称为 `Go 主协程`（Main Goroutine）。
运行该程序，会发现，上述程序只输出了"main function"。这是为什么呢？这里先解释下，Go 协程的两个主要性质。

### 协程的2个主要性质
```
1.启动一个新的协程时，协程的调用会立即返回。与函数不同，程序控制不会去等待 Go 协程执行完毕。在调用 Go 协程之后，程序控制会立即返回到代码的下一行，忽略该协程的任何返回值。
2.如果希望运行其他 Go 协程，Go 主协程必须继续运行着。如果 Go 主协程终止，则程序终止，于是其他 Go 协程也不会继续运行。
```
现在你应该能够理解，为何我们的 Go 协程没有运行了吧。在第 11 行调用了` go hello()` 之后，程序控制没有等待 `hello` 协程结束，立即返回到了代码下一行，打印 `main function`。接着由于没有其他可执行的代码，Go 主协程终止，于是 `hello` 协程就没有机会运行了。

修复这个问题，来让hello函数输出。
```go
package main

import (
   "fmt"
   "time"
)

func hello() {
   fmt.Println("Hello world goroutine")
}
func main() {
   go hello()
   time.Sleep(1 * time.Second) //休眠1秒
   fmt.Println("main function")
}
```
在上面程序的第 13 行，我们调用了 time 包里的 `Sleep函数`，该函数会`休眠`执行它的 Go 协程。在这里，我们使 Go 主协程休眠了 1 秒。因此在主协程终止之前，调用 `go hello()` 就有足够的时间来执行了。该程序首先打印 `Hello world goroutine`，等待 1 秒钟之后，接着打印 `main function`。

在 Go 主协程中使用休眠，以便等待其他协程执行完毕，这种方法`只是用于`理解 Go 协程如何工作的技巧。工作中常用到的方式是通过`信道`，它可用于在其他协程结束执行之前，阻塞 Go 主协程。

### 启动多个协程

```go

package main

import (
   "fmt"
   "time"
)

func numbers() {
   for i := 1; i <= 5; i++ {
      time.Sleep(250 * time.Millisecond)
      fmt.Printf("%d ", i)
   }
}
func alphabets() {
   for i := 'a'; i <= 'e'; i++ {
      time.Sleep(400 * time.Millisecond)
      fmt.Printf("%c ", i)
   }
}
func main() {
   go numbers()
   go alphabets()
   time.Sleep(3000 * time.Millisecond)
   fmt.Println("main terminated")
}
```

上述代码，启动了两个 Go 协程。现在，这两个协程并发地运行。`numbers` 协程首先休眠 250 微秒，接着打印 `1`，然后再次休眠，打印 `2`，依此类推，一直到打印 `5` 结束。`alphabete` 协程同样打印从 `a` 到 `e` 的字母，并且每次有 400 微秒的休眠时间。 Go 主协程启动了 `numbers` 和 `alphabete` 两个 Go 协程，休眠了 3000 微秒后终止程序。
输出结果为：
```go
1 a 2 3 b 4 c 5 d e main terminated
```
图解
image


第一张蓝色的图表示 `numbers` 协程，第二张褐红色的图表示 `alphabets` 协程，第三张绿色的图表示 Go 主协程，而最后一张黑色的图把以上三种协程合并了，表明程序是如何运行的。在每个方框顶部，诸如 `0 ms` 和 `250 ms` 这样的字符串表示时间（以微秒为单位）。在每个方框的底部，1、2、3 等表示输出。蓝色方框表示：250 ms 打印出 1，500 ms 打印出 2，依此类推。最后黑色方框的底部的值会是 `1 a 2 3 b 4 c 5 d e main terminated`，这同样也是整个程序的输出。以上图片非常直观，你可以用它来理解程序是如何运作的。

------

## 信道(Channel)

### 什么是信道?
`信道`可以想象成 Go `协程`之间通信的`管道`。如同管道中的水会从一端流到另一端，通过使用信道，数据也可以从一端`发送`，在另一端`接收`。

### 如何声明信道?
所有信道都`关联`了一个`类型`。信道`只能`运输这种类型的数据，而运输其他类型的数据都是`非法的`。
`chan T` 表示 `T` 类型的`信道`。
信道的零值为 `nil`。可以用 `make` 来定义信道。
```go
package main

import "fmt"

func main() {
   //声明一个 int 类型的信道 a
   var a chan int
   //或者使用 make
   b := make(chan string)
   if a == nil {
      fmt.Println("channel a is nil, going to define it")
      a = make(chan int)
      fmt.Printf("Type of a is %T \n", a)
      fmt.Printf("Type of b is %T", b)
   }
}
```
### 信道接收和发送数据
```go
data := <- a // 读取信道 a  
a <- data // 写入信道 a
```
信道旁的`箭头方向`指定了是发送数据还是接收数据。
在第一行，箭头对于 `a` 来说是`向外指`的，因此我们`读取`了信道 a 的值，并把该值`存储`到变量 `data`。

在第二行，箭头`指向`了 `a`，因此我们在把数据`写入`信道 a。

### 信道默认是阻塞的
对`信道`而言，无论是发送与接收，默认都是`阻塞的`。
这是什么意思？
当把数据发送到信道时，程序控制会在发送数据的语句处发生阻塞，直到有其它 Go 协程从信道读取到数据，才会解除阻塞。与此类似，当读取信道的数据时，如果没有其它的协程把数据写入到这个信道，那么读取过程就会一直阻塞着。
```go
package main

import (
   "fmt"
)

func hello(done chan bool) {
   fmt.Println("Hello world goroutine")
   done <- true
}
func main() {
   done := make(chan bool)
   go hello(done)
   <-done
   fmt.Println("main function")
}
```
在上述代码里，我们在第 12 行创建了一个 bool 类型的信道 `done`，并把 `done` 作为参数传递给了 `hello` 协程。在第 14 行，我们通过信道 `done` 接收数据。这一行代码发生了`阻塞`，除非有协程向 `done` 写入数据，否则程序不会跳到下一行代码。于是，这就不需要用以前的 `time.Sleep` 来阻止 Go 主协程退出了。

`<-done` 这行代码通过信道`done` 接收数据，但并没有使用数据或者把数据存储到变量中。这完全是合法的。

现在我们的 Go 主协程发生了`阻塞`，等待信道 `done` 发送的数据。该信道作为参数传递给了协程 `hello`，`hello` 打印出 `Hello world goroutine`，接下来向 `done` 写入数据。当完成写入时，Go 主协程会通过信道 `done` 接收数据，于是它解除阻塞状态，打印出文本 `main function`。



### 什么是缓冲信道?

信道默认是无缓冲信道，无缓冲信道的发送和接收过程是阻塞的。

那什么是缓冲信道呢？

`缓冲信道`，即表示创建一个`有缓冲`（Buffer）的信道。只在`缓冲已满`的情况，才会阻塞向缓冲信道（Buffered Channel）`发送`数据。同样，只有在`缓冲为空`的时候，才会阻塞从缓冲信道`接收`数据。

即`无缓冲通道为同步通信，有缓冲通道为异步通信`。

通过向 `make` 函数再传递一个表示容量的`参数`（指定缓冲的大小），可以创建缓冲信道。

```go
ch := make(chan type, capacity)
```

要让一个信道有缓冲，上面语法中的 `capacity` 应该大于 0。无缓冲信道的容量`默认`为 0，为0时可以省略。




### channel的特性口诀

```go

1.往一个 nil channel 发送数据，造成永远阻塞

2.从一个 nil channel 接收数据，造成永远阻塞

3.往一个已经关闭的 channel 发送数据，会引起 panic

4.从一个已经关闭的 channel 接收数据，如果缓冲区中为空，则返回一个零值

5.无缓冲的channel是同步的，而有缓冲的channel是非同步的

通过口诀来记忆："空读写阻塞，写关闭异常，读关闭空零"

```
