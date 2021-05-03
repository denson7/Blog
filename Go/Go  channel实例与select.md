[TOC]
## Channel实例

### 用channel实现先后顺序打印
```go
package main

import (
   "fmt"
   "time"
)
//定义一个管道 类型为int
var ch = make(chan int)

//定义一个打印机，参数为字符串，按每个字符挨次打印
func Printer(str string) {
   for _, data := range str {
      fmt.Printf("%c", data)
      time.Sleep(time.Second)
   }
   fmt.Printf("\n")
}

//person1执行完后，才能到person2执行
func person1() {
   Printer("hello")
   //发送，给管道发送数据
   ch <- 666
}

func person2() {
   //接收：从管道取数据，如果管道没有数据就会阻塞，一直等到
   <-ch
   Printer("world")
}
func main() {
   //新建2个协程，同时使用打印机
   go person1()
   go person2()
   for {
   }
}
```
### 用channel实现交替打印
```go
package main

import "fmt"

var chan1 = make(chan int)
var chan2 = make(chan string)
var index = make(chan bool)

func func1() {
   for i := 0; i < 26; i++ {
      chan1 <- i
      fmt.Print(i)
      <-chan2
   }
}

func func2() {
   for i := 'A'; i <= 'Z'; i += 1 {
      <-chan1
      fmt.Print(string(i))
      chan2 <- string(i)
   }

   index <- true
}

func main() {
   go func1()
   go func2()

   <-index
}
```
### 关闭channel
```go
package main

import "fmt"

func main() {
   // 关闭channel
   ch := make(chan int)
   go func() {
      for i := 0; i < 5; i++ {
         ch <- i
      }
      // 写入端主动关闭channel
      close(ch)
   }()

   for {
      // 判断channel是否关闭
      // ok为false,则对端(这里是写入端)已经关闭channel, num无数据
      // ok为true, 则对端(这里是写入端)没有关闭channel, num为写入的数据
      if num, ok := <-ch; ok == true {
         fmt.Println("读到数据:", num)
      } else {
         // channel中没有数据就跳出循环
         break
      }
      // 注意: 1.数据没有发送完不应该关闭channel; 2.已经关闭的channel不能再向它写入数据; 3.已经关闭的channel可以从中读取数据
   }
}
```
### 单向channel
```go
package main

import "fmt"

func send(out chan<- int) {
   out <- 100
   close(out)
}
func recv(in <-chan int) {
   n := <-in
   fmt.Print(n)
}

func main() {
   // channel 默认是双向的 ch := make(chan int)
   // 单向写channel ch := make(chan<- int)
   // 单向读channel ch := make(<-chan int)
   // 注意1: 双向channel可以隐式转换为任意一种单向channel;
   // 注意2: 单向channel不能转换为双向channel;

   ch := make(chan int) // 双向channel
   //var sendCh chan<- int = ch // 单向写channel
   //sendCh <- 999

   //var revCh <-chan int = ch // 单向读channel
   //num := <-revCh
   //fmt.Print(num) // fatal error: all goroutines are asleep - deadlock!
   // 这里死锁的原因是由于单向channel,阻塞了,channel通信必须要求一个读一个写,单独出现读或写都会出现死锁

   go func() {
      // 单向channel传参
      send(ch) // 双向channel转换为单向channel
   }()

   recv(ch)
}
```
###select用法
`select`是Golang中的一个关键字,通过select可以监听channel中的数据流动。用法与switch语言类似，不同的是在select中对应的case语句必须是一个IO操作。
```go
select 只能用于 channel 的操作(写入/读出)，而 switch 则更通用一些；
select 的 case 是随机的，而 switch 里的 case 是顺序执行；
select 要注意避免出现死锁，同时也可以自行实现超时机制；
select 里没有类似 switch 里的 fallthrough 的用法；
select 不能像 switch 一样接函数或其他表达式。
```
示例
```go
chan1 := make(<-chan int)
chan2 := make(chan<- int)
select {
case <-chan1:
   // 如果chan1成功读取到数据,则进行该case处理语句
case chan2 <- 1:
   // 如果成功向chan2写入数据,则进行该case处理语句
default: // 注意:在循环中一般不写default,避免忙轮询
   // 如果上面都没有成功,则进入default处理流程
}
```
#### select 的超时设置
```go
//无缓冲通道读
func ReadNoDataFromNoBufChWithSelect() {
   noBufCh := make(chan int)
   //阻塞场景1：通道中无数据，但对通道进行读操作
   //<-noBufCh
   //fmt.Println("read from no buffer channel success") // fatal error: all goroutines are asleep - deadlock!

   if v, err := ReadWithSelect(noBufCh); err != nil {
      fmt.Println(err)
   } else {
      fmt.Printf("read: %d\n", v)
   }

   // Output:
   // channel has no data
}

//无缓冲通道写
func WriteNoBufChWithSelect() {
   ch := make(chan int)
   //阻塞场景2：通道中无数据，向通道写数据，但无协程读取
   //ch <- 1
   //fmt.Println("write success no block") // fatal error: all goroutines are asleep - deadlock!
   if err := WriteChWithSelect(ch); err != nil {
      fmt.Println(err)
   } else {
      fmt.Println("write success")
   }

   // Output:
   // channel blocked, can not write
}

// 有缓冲通道读
func ReadNoDataFromBufChWithSelect() {
   bufCh := make(chan int, 1)
   //阻塞场景1：通道的缓存无数据，但对通道进行读操作
   //<-bufCh
   //fmt.Println("read from no buffer channel success") // fatal error: all goroutines are asleep - deadlock!
   if v, err := ReadWithSelect(bufCh); err != nil {
      fmt.Println(err)
   } else {
      fmt.Printf("read: %d\n", v)
   }

   // Output:
   // channel has no data
}

// 有缓冲通道写
func WriteBufChButFullWithSelect() {
   ch := make(chan int, 1)
   // make ch full
   ch <- 100
   //阻塞场景2：通道的缓存已经占满，继续向通道写数据，但无协程读。
   //ch <- 1
   //fmt.Println("write success no block") // fatal error: all goroutines are asleep - deadlock!

   if err := WriteChWithSelect(ch); err != nil {
      fmt.Println(err)
   } else {
      fmt.Println("write success")
   }

   // Output:
   // channel blocked, can not write
}

// select结构实现通道读
func ReadWithSelect(ch chan int) (x int, err error) {
   timeout := time.NewTimer(time.Microsecond * 500)
   select {
   case x = <-ch:
      return x, nil
   case <-timeout.C:
      return 0, errors.New("read time out")
   }
}
// select结构实现通道写
func WriteChWithSelect(ch chan int) error {
   timeout := time.NewTimer(time.Microsecond * 500)

   select {
   case ch <- 1:
      return nil
   case <-timeout.C:
      return errors.New("write time out")
   }
}
```
```go
package main

import (
   "fmt"
   "time"
)

func makeTimeout(ch chan bool, t int) {
   time.Sleep(time.Second * time.Duration(t))
   ch <- true
}

func main() {
   c1 := make(chan string, 1)
   c2 := make(chan string, 1)
   timeout := make(chan bool, 1)

   go makeTimeout(timeout, 2)

   // 当 case 里的信道始终没有接收到数据时，而且也没有 default 语句时，select 整体就会阻塞。
   // 但有时我们并不希望 select 一直阻塞下去，这时候就可以手动设置一个超时时间
   select {
   case msg1 := <-c1:
      fmt.Println("c1 received: ", msg1)
   case msg2 := <-c2:
      fmt.Println("c2 received: ", msg2)
   case <-timeout:
      fmt.Println("Timeout, exit.")
   }
}
```

实现一个斐波拉契数列
```go
package main

import (
   "fmt"
)

func fibonacci(ch chan<- int, quit <-chan bool) {
   x, y := 1, 1
   for {
      //监听channel数据的流动
      select {
      case ch <- x:
         x, y = y, x+y
      case flag := <-quit:
         fmt.Println("flag = ", flag)
         return
      }
   }
}

func main() {
   //数字通信
   ch := make(chan int)
   //程序是否结束
   quit := make(chan bool)

   //新建协程,从channel中读数据
   go func() {
      for i := 0; i < 8; i++ {
         //没有数据时，会阻塞，直到通道内有数据
         num := <-ch
         fmt.Println("num = ", num)
      }
      quit <- true
   }()

   //生产者，产生数字，写入channel
   fibonacci(ch, quit)
}
```
实现一个超时，退出程序
```go
package main

import (
   "fmt"
   "time"
)

func main() {
   //数字通信
   ch := make(chan int)
   //程序是否结束
   quit := make(chan bool)

   go func() {
      for {
         select {
         case num := <-ch:
            fmt.Println("num = ", num)
         case <-time.After(3 * time.Second):
            fmt.Println("超时")
            quit <- true
         }
      }
   }()

   for i := 0; i < 5; i++ {
      ch <- i
      time.Sleep(time.Second)
   }

   <-quit
   fmt.Println("程序结束")
}
```
### 协程同步
```go
package main

import (
   "fmt"
   "runtime"
   "sync"
   "time"
)
var WG sync.WaitGroup

// 读取数据
func Read()  {
   for i := 0; i < 3; i++ {
      WG.Add(1)
   }
}
// 写入数据
func Write()  {
   for i := 0; i < 3; i++ {
      time.Sleep(time.Second * 2)
      fmt.Println("done->", i)
      WG.Done()
   }
}

func main() {
   runtime.GOMAXPROCS(runtime.NumCPU() - 1)
   fmt.Printf(" cpn num = %d", runtime.NumCPU())
   //系统工具：sync.WaitGroup
   //Add(delta int) 添加协程记录
   //Done() 移除协程记录
   //Wait() 同步等待所有记录的协程全部结束
   Read()
   go Write()
   WG.Wait()
   fmt.Println("All Done!")
}
```
### 协程异步
```go
package main

import (
   "fmt"
   "runtime"
   "sync"
)
var wg = sync.WaitGroup{}

// 方式2：无缓冲channel与任务发送/执行分离方式
func task() {
   ch := make(chan int) // 无buffer channel
   goCnt := 3           // 启动goroutine的数量
   for i := 0; i < goCnt; i++ {
      go doing(ch)
   }
   taskCnt := 10 // 模拟用户需求业务的数量
   for t := 0; t < taskCnt; t++ {
      // 发送任务
      sendTask(t, ch)
   }
   wg.Wait()
}

func doing(ch chan int) {
   for t := range ch {
      fmt.Println("go task = ", t, ", goroutine count = ", runtime.NumGoroutine())
      wg.Done()
   }
}

func sendTask(task int, ch chan int) {
   wg.Add(1)
   ch <- task
}


// 方式1：channel与sync同步组合方式
func test() {
   // 缓冲数量为3,即控制协程数量
   ch := make(chan int, 3)
   for i := 0; i < 10; i++ {
      wg.Add(1)
      ch <- i
      go doSomething(ch, i)
   }
   wg.Wait()
}

func doSomething(ch chan int, i int) {
   // runtime.NumGoroutine()这里为4的原因是因为还有一个main主协程
   fmt.Println("go test ", i, " goroutine count = ", runtime.NumGoroutine())
   <-ch
   wg.Done()
}

func main() {
   task()
   test()
}
```
### 工作池
```go
package main

import (
   "fmt"
   "time"
)

func worker(i int, jobs <-chan int, results chan<- int) {
   for j := range jobs {
      fmt.Println("worker", i, "processing job", j)
      time.Sleep(time.Second)
      results <- j *2
   }
}

func main() {
   jobs := make(chan int, 100)
   results := make(chan int, 100)
   // 设置协程个数
   taskNum := 3
   // 启动3个worker协程，一开始的时候worker阻塞执行，因为jobs通道里面还没有工作任务
   for i := 0; i < taskNum; i++ {
      go worker(i, jobs, results)
   }
   // 发送任务，然后关闭通道，告知任务发送完成
   for i := 0; i < 10; i++ {
      jobs<-i
   }
   close(jobs)

   // 从results里面获得结果
   for a := 0; a < 10; a++ {
      <-results
   }
}
```
