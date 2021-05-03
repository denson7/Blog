[TOC]
## panic
当程序遇到`致命错误`导致无法继续运行时就会出发 panic , 程序会`终止`，不会继续往下运行。例如：数组越界，空指针等。

```go
package main

import "fmt"

func main() {
   s := []int{1, 2, 3}
   for i := 0; i <= 4; i++ {
      fmt.Println(s[i])
   }
}
```
```go
package main

import (
   "errors"
)
//主动调用panic函数
func panicFunc() {
   panic(errors.New("this is test for panic"))
}

func main() {
   panicFunc()
}
```
## recover
recover 函数能使当前程序从 panic 中恢复。recover 能够拦截 panic 事件，使得程序不会因为意外而触发 panic 事件而完全退出。

recover 函数返回的是一个 interface{} 类型的结果，如果捕获到了 panic 事件，该结果就为非 nil。

例如：

```go

package main

import (
   "errors"
   "fmt"
)

func panicFunc() {
   defer func() {
      if p := recover(); p != nil {
         fmt.Println("recover panic")
      }
   }()

   panic(errors.New("this is test for panic"))
}

func main() {
   fmt.Println("before panic")

   panicFunc()

   fmt.Println("after panic")
}
```



## Error 错误处理
如果一个`函数`或`方法`返回了错误，按照惯例，错误会作为最后一个值返回按照 Go 的惯例，在处理错误时，通常都是将返回的错误与 `nil` 比较。`nil` 值表示了`没有错误`发生，而`非 nil` 值表示出现了`错误`。

```go
package main

import (
   "fmt"
   "net"
   "os"
)

func main() {
   // 最常用的错误信息输出
   //Open 函数会返回一个文件句柄和一个值为 nil 的错误,按照惯例，如果方法返回错误，它会作为最后一个值返回
   f, err := os.Open("/test.txt")
   if err != nil { //如果打开文件发生了错误，会返回一个不等于 nil 的错误
      fmt.Println(err)
      return
   }
   fmt.Println(f.Name(), "opened successfully")

   
   d, err := os.Open("/test.txt")
   //获取更多的错误信息,从底层结构体字段获取更多信息
   if err, ok := err.(*os.PathError); ok {
      fmt.Println("File at path", err.Path, "failed to open")
      return
   }

   fmt.Println(d.Name(), "opened successfully")

   addr, err := net.LookupHost("test.com")
   if err, ok := err.(*net.DNSError); ok {
      if err.Timeout() {
         fmt.Println("operation timed out")
      } else if err.Temporary() {
         fmt.Println("temporary error")
      } else {
         fmt.Println("generic error: ", err)
      }
      return
   }
   fmt.Println(addr)
}
```

