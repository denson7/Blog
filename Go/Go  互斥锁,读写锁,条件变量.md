[TOC]
## 死锁
注意，死锁不是一种锁，而是一种错误，是使用锁导致的现象。
```go
package main

func main() {
   // 死锁1: 单go程自己造成死锁
   //ch := make(chan int)
   //ch <- 777   // 这里写数据,会阻塞
   //num := <-ch // 这里实际是执行不到,所以造成死锁
   // 要点1: 使用channel应该在至少2个以上的go程进行通信,否则死锁
   //fmt.Println("num:", num)

   // 死锁2: go程间channel访问顺序造成死锁
   //ch := make(chan int)
   //num := <-ch // 这里读数据,会阻塞,后续代码也不会执行, 所以死锁
   //fmt.Println("num:", num)

   //go func() {
   // ch <- 777
   //}()

   // 修改读取数据的位置
   //num := <-ch // 放在这里进行读数据
   //fmt.Println("num:", num)
   // 要点2: 使用channel一端读(写)操作,要保证另一端写(读)操作,同时有机会执行,否则死锁

   //死锁3: 多go程,多 channel 交叉死锁
   ch1 := make(chan int)
   ch2 := make(chan int)

   go func() { // 子
      for {
         select {
         case num := <-ch1:
            ch2 <- num
         }
      }
   }()

   for {
      select {
      case num := <-ch2:
         ch1 <- num

      }
   }
}
```
golang中`sync`包实现了两种锁`Mutex （互斥锁）`和`RWMutex（读写锁）`，其中RWMutex是基于Mutex实现。
## 互斥锁  Mutex
### 什么是互斥锁
`互斥锁`：是并发程序对`共享资源`进行访问控制时的主要方式。在访问共享资源之前对进行加锁操作，在访问完成之后进行解锁操作。 
加锁后，任何其他试图再次加锁的线程会被`阻塞`，直到当前进程解锁。类似生活中高铁上的卫生间，一个乘客使用时应该锁门，使用后再开门。
### 互斥锁的方法
标准库`sync`中定义了一个叫`Mutex`的结构体，它只有两个方法：`Lock`加锁 和`Unlock`解锁。
### 适用场景
适用于不确定场景`读写次数没有明显的区别`或`读少写多`的场景，它只允许只有一个读或者写的场景，所以该锁叶叫做`全局锁`。 



### 互斥锁的使用

```go
package main

import (
   "fmt"
   "math/rand"
   "sync"
   "time"
)

var lock sync.Mutex

func testMutex() {
   var a map[int]int
   a = make(map[int]int, 5)

   a[8] = 10
   a[3] = 10
   a[2] = 10
   a[1] = 10
   a[18] = 10
   fmt.Println(a)
   for i := 0; i < 2; i++ {
      go func(b map[int]int) {
         lock.Lock()
         b[8] = rand.Intn(100)
         lock.Unlock()
      }(a)
   }

   lock.Lock()
   fmt.Println(a)
   lock.Unlock()

   time.Sleep(time.Second)
}

func main() {
   testMutex()
}
```
```go
package main

import (
   "fmt"
   "sync"
   "time"
)

//var chan11 = make(chan int)
//
//func pt(str string) {
// for _, v := range str {
//    fmt.Print(string(v))
//    time.Sleep(time.Millisecond * 200)
// }
//}
//
//func person1() {
// pt("hello")
// chan11 <- 100
//}
//
//func person2() {
// <-chan11
// pt("world")
//}
//
//func main() {
// go person1()
// go person2()
//  time.Sleep(5 * time.Second)
//}

// 使用互斥锁
// 创建一个互斥量,新建的互斥锁状态为0. 锁只有一把
var mutex sync.Mutex

func pt(str string) {
   // 访问共享数据之前进行加锁
   mutex.Lock()
   for _, v := range str {
      fmt.Print(string(v))
      time.Sleep(time.Millisecond * 200)
   }
   // 访问共享数据结束进行解锁
   mutex.Unlock()
}

func person1() { // 先打印
   pt("hello")
}

func person2() { // 后打印
   pt("world")
}

func main() {
   go person1()
   go person2()
   time.Sleep(5 * time.Second)
}
```
## 读写锁  RWMutex
### 什么是读写锁
`读写锁`：它把对`共享资源`的访问操作划分成`读操作`和`写操作`。它其实是针对于读写操作的一种互斥锁。它与普通的互斥锁最大的不同就是，它可以分别针对读操作和写操作进行锁定和解锁操作。
也就是说，`当一个goroutine进行写操作的时候，其他goroutine既不能进行读操作，也不能进行写操作`。
读写锁有`三种状态`：`读加锁状态`、`写加锁状态`和`不加锁状态` 。

```
1.当处在读加锁状态时，任何线程都可以对其进行读加锁操作，但是所有试图进行写加锁操作的线程都会被阻塞，直到所有的读线程都解锁。 
2.当处在写加锁状态下，任何试图对这个锁进行加锁的线程都会被阻塞(无论读还是写都会被阻塞)，直到写进程对其解锁。
3.当处在不加锁状态，任何线程都可以对其进行读写操作。
```
### 读写锁的方法
标准库`sync`中定义了一个叫`RWMutex`的结构体，它有四个方法：`RLock`读加锁 ，`RUnlock`读解锁，`Lock`写加锁，`Unlock`写解锁。
### 使用场景
`读写锁`经常用于`读多于写`次数的场景。



### 读写锁的使用

```go
package main

import (
   "fmt"
   "sync"
   "sync/atomic"
   "time"
)

var rwLock sync.RWMutex

func testRWLock() {
   var a map[int]int
   a = make(map[int]int, 5)
   var count int32
   a[8] = 10
   a[3] = 10
   a[2] = 10
   a[1] = 10
   a[18] = 10

   for i := 0; i < 10; i++ {
      go func(b map[int]int) {
         for {
            rwLock.RLock()
            time.Sleep(time.Millisecond)
            fmt.Println(a)
            rwLock.RUnlock()
            atomic.AddInt32(&count, 1)
         }
      }(a)
   }
   time.Sleep(time.Second * 3)
   fmt.Println(atomic.LoadInt32(&count))
}

func main() {
   testRWLock()
}
```
### 条件变量
`条件变量`：条件变量的作用是在对应的共享数据的状态发生变化时，通知阻塞在某个条件上的协程。注意，`条件变量并不是锁`。往往条件变量总是与锁一起使用。
例如:
在生产者消费者模型中，如果存在多个生产者和多个消费者，会出现消费者打印的数据和生产者生产的数据顺序不一致的情况，如果解决这个问题呢，可以通过加锁的方式来进行解决。如果队列满了，可以使用条件变量让生产者对应的goroutine暂停(阻塞)，当消费者消费后，队列不再满了时，应该唤醒(通知)阻塞的生产者goroutine继续生产。
Go标准库中的sync.Cond类型代表了条件变量。对应的有3个常用方法: Wait，Signal,Broadcast
````go
func (c *Cond) Wait() {}
// 该函数的作用有如下3点
// a.阻塞等待条件变量满足
// b.释放已掌握的互斥锁相当于cond.L.Unlock, 注意: 两步为一个原子操作
// c.当被唤醒,Wait()函数返回时,解除阻塞并重新获取互斥锁,相当于cond.L.Lock()

func (c *Cond) Signal() {}
// 单发通知,给一个正等待(阻塞)在该条件变量上的goroutine发送通知

func (c *Cond) Broadcast() {}
// 广播通知,给正在等待(阻塞)在该条件变量上的所有goroutine发送通知
```
示例
```go
// 多个生产者多个消费者模式
package main

import (
   "fmt"
   "math/rand"
   "time"
)

func producer2(out chan<- int, idx int) {
   for i := 0; i < 50; i++ {
      num := rand.Intn(800)
      fmt.Printf("生产者%dth，生产：%d\n", idx, num)
      out <- num
   }
   close(out)
}

func consumer2(in <-chan int, idx int) {
   for num := range in {
      fmt.Printf("-----消费者%dth，消费：%d\n", idx, num)
   }
}

func main() {
   product := make(chan int)
   rand.Seed(time.Now().UnixNano())

   for i := 0; i < 5; i++ {
      go producer2(product, i+1) // 1 生产者
   }
   for i := 0; i < 5; i++ {
      go consumer2(product, i+1) // 3 个消费者
   }
   for {

   }
}
// 条件变量
package main

import (
   "fmt"
   "math/rand"
   "sync"
   "time"
)

var cond sync.Cond // 定义全局条件变量

func producer08(out chan<- int, idx int) {
   for {
      // 先加锁
      cond.L.Lock()
      // 判断缓冲区是否满
      for len(out) == 5 {
         cond.Wait() // 1. 2. 3.
      }
      num := rand.Intn(800)
      out <- num
      fmt.Printf("生产者%dth，生产：%d\n", idx, num)
      // 访问公共区结束，并且打印结束，解锁
      cond.L.Unlock()
      // 唤醒阻塞在条件变量上的 消费者
      cond.Signal()
      time.Sleep(time.Millisecond * 200)
   }
}

func consumer08(in <-chan int, idx int) {
   for {
      // 先加锁
      cond.L.Lock()
      // 判断 缓冲区是否为空
      for len(in) == 0 {
         cond.Wait()
      }
      num := <-in
      fmt.Printf("-----消费者%dth，消费：%d\n", idx, num)
      // 访问公共区结束后，解锁
      cond.L.Unlock()
      // 唤醒 阻塞在条件变量上的 生产者
      cond.Signal()
      time.Sleep(time.Millisecond * 200)
   }
}

func main() {
   product := make(chan int, 5)
   rand.Seed(time.Now().UnixNano())

   quit := make(chan bool)

   // 指定条件变量 使用的锁
   cond.L = new(sync.Mutex) // 互斥锁 初值 0 ， 未加锁状态

   for i := 0; i < 5; i++ {
      go producer08(product, i+1) // 1 生产者
   }
   for i := 0; i < 5; i++ {
      go consumer08(product, i+1) // 3 个消费者
   }
   /* for {
      runtime.GC()
   }*/
   <-quit

}
```
