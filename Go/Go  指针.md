[TOC]

## 指针

```go
& 表示取地址
* 表示根据地址取值
```
`指针`存储一个变量的地址。
指针用`*`后面`加类型`的方式表示，例如：`*int`
用`&`取一个变量的地址。用`*`访问指针所指的变量，例如

```go
package main

import "fmt"

func main() {
b := 255 //定义一个变量
var a *int = &b // &b 获取变量b的地址，a表示一个指针，即a指向了b
fmt.Printf("Type of a is %T\n", a) //打印 a 的类型
fmt.Println("address of b is", a) //打印 a 的值，会输出 b 的地址
*a = 100 
fmt.Println(b) // 100
}
```
指针的0值为nil。

### 指针的解引用
指针的解引用可以获取指针所指向的变量的值。将 `a` 解引用的语法是 `*a`。

```go
package main

import "fmt"

func main() {
a := 255
b := &a //获取变量a的地址

fmt.Println("address of e is", b)
fmt.Println("value of f is", *b) //指针的解引用

}
```

### 指针数组和数组指针
```go
指针数组是一个数组，数组中的元素是指针。
数组指针是一个指针，这个指针指向一个数组。
func PointArr()  {
   // 指针数组
   a, b := 1, 2
   pointArr := [...]*int{&a, &b}
   fmt.Println("指针数组, pointArr:", pointArr) //  [0xc0000a0068 0xc0000a0080]

   // 数组指针
   arr := [...]int{3,4,5}
   arrPoint := &arr
   fmt.Println("数组指针, arrPoint:", arrPoint) //  &[3 4 5]
}

```









