[TOC]
Go 语言是一种`静态类型`的编程语言，所以在编译器进行编译的时候，就要知道每个值的类型，即不能在运行期改变变量类型。

```go
# 基本数据类型
整型， 浮点型，布尔型，字符串
# 复合数据类型
数组，切片，结构体，函数，map，channel，接口等
// 说明： 字节：也叫Byte，是计算机的基本存储单位。8 bit = 1 Byte； 1024 Byte = 1 KB，例如一个中文占2个字节。
```
## 基本类型
```go
bool      the set of boolean (true, false)

uint8      the set of all unsigned  8-bit integers (0 to 255)
uint16      the set of all unsigned 16-bit integers (0 to 65535)
uint32      the set of all unsigned 32-bit integers (0 to 4294967295)
uint64      the set of all unsigned 64-bit integers (0 to 18446744073709551615)

int8      the set of all signed  8-bit integers (-128 to 127)
int16      the set of all signed 16-bit integers (-32768 to 32767)
int32      the set of all signed 32-bit integers (-2147483648 to 2147483647)
int64      the set of all signed 64-bit integers (-9223372036854775808 to 9223372036854775807)

float32      the set of all IEEE-754 32-bit floating-point numbers
float64      the set of all IEEE-754 64-bit floating-point numbers

complex64      the set of all complex numbers with float32 real and imaginary parts
complex128      the set of all complex numbers with float64 real and imaginary parts

byte      alias for uint8
rune      alias for int32
uint      either 32 or 64 bits
int      same size as uint
uintptr      an unsigned integer large enough to store the uninterpreted bits of a pointer value

string      the set of string value (eg: "hello")
```
**`零值`**，并非是空值，而是一种“变量未填充前”的默认值，通常为0。

```go
int     0
int8    0
int32   0
int64   0
uint    0x0
rune    0 //rune的实际类型是 int32
byte    0x0 // byte的实际类型是 uint8
float32 0 //长度为 4 byte
float64 0 //长度为 8 byte
bool    false
string  ""
```

### 布尔类型
Go中，布尔值的类型为`bool`，值是`true`或`false`，默认为`false`。
```
//示例代码
var isActive bool  // 全局变量声明
var enabled, disabled = true, false  // 忽略类型的声明
func test() {
   var available bool  // 一般声明
   valid := false      // 简短声明
   available = true    // 赋值操作
}
```
### 数字类型
`整数类型` ：分`无符号`和`有符号`两种。Go同时支持`int`和`uint`，这两种类型的长度相同，但具体长度取决于不同编译器的实现。
Go里面也有直接定义好位数的类型：`rune, int8, int16, int32, int64`和`byte, uint8, uint16, uint32, uint64`。
其中`rune`是`int32`的别称，`byte`是`uint8`的别称。
```
byte 和 uint8 没有区别，rune 和 int32 没有区别，那为什么还要多出 byte 和 rune 类型呢？
理由很简单，因为uint8 和 int32 ，直观上让人以为这是一个数值，但是实际上，它也可以表示一个字符，所以为了消除这种直观错觉，就诞生了 byte 和 rune 这两个别名类型。
byte代表UTF-8中字符串的单个字节的值.
```


例如：测试变量 a 和 b 的类型和大小。格式说明符 `%T` 用于打印类型，而 `%d` 用于打印字节大小。在 `Printf` 方法中，使用 `%T `格式说明符（Format Specifier），可以`打印出变量的类型`。Go 的 `unsafe` 包提供了一个 `Sizeof` 函数，该函数接收变量并`返回它的字节大小`。unsafe 包应该小心使用，因为使用 unsafe 包可能会带来可移植性问题。
```go
package main

import (
   "fmt"
   "unsafe"
)

func main() {
   var a int = 89
   b := 95
   fmt.Println("value of a is", a, "and b is", b)
   fmt.Printf("type of a is %T, size of a is %d", a, unsafe.Sizeof(a))   // a 的类型和大小
   fmt.Printf("\ntype of b is %T, size of b is %d", b, unsafe.Sizeof(b)) // b 的类型和大小
}
```
以上代码输出：
```go
value of a is 89 and b is 95
type of a is int, size of a is 8
type of b is int, size of b is 8
```
推断出 a 和 b 为 int 类型，且大小都是 64位（8 字节）。如果你在 32位系统上运行上面的代码，会有不同的输出。在 32位系统下，a 和 b 会占用 32 位（4 字节）的大小。

`浮点数类型`：分`float32`和`float64`两种（没有float类型），默认是`float64`。

`复数类型`：Go还支持复数。分`complex128`和`complex64`两种，它的默认类型是`complex128`（float64位实数+float64位虚数）。`complex64`是(float32位实数+float32位虚数)。复数的形式为`RE + IMi`，其中`RE`是实数部分，`IM`是虚数部分，而最后的`i`是虚数单位。

```go
var c complex64 = 5+5i
//output: (5+5i)
fmt.Printf("Value is: %v", c)
```
#### 浮点数精度丢失问题
几乎所有的编程语言都有精度丢失这个问题。在定长条件下，二进制小数和十进制小数互转就可能存在精度丢失。使用第三方包解决（github.com/shopspring/decimal）。
```go
var d float64 = 1129.6
fmt.Println(d * 100) // 112959.99999999
```
### 字符串类型
```go
在golang一共存在3种字符类型: string []byte 和 []rune。
# 1. 其中string 最好理解，就是字面含义上的字符, 如 s := "字"

# 2. []rune 代表的则是字符的unicode编码值，它表示的是一个 Unicode字符，等同于int32，常用于处理unicode。如
r := []rune("字")
fmt.Print(r)
打印出的结果是 [23383], 这个就是汉字 “字” 的Unicode编码值的10进制表达。
如果转换成16进制的结果是5B57。
hex := "\u5B57"fmt.Print(hex) //输出 字
# 3.[]byte 代表的是按照utf-8格式对字符的unicode值进行编码后的结果。它表示的是 ACSII 表中的一个字符。等同于int8，常用于处理ascii字符。
s := "字"
fmt.Println([]byte(s))
输出的结果[229 173 151], 我们将这个结果转化成 2进制则是 [11100101, 10101101, 10010111]。

s2 := []byte("a")
fmt.Println(s2) // 输出 97
//
Unicode 为每一个字符规定了一个唯一值， 通常标准的字符都位于 \u0000到\uFFFF 65536个值， 当然仍可以继续扩充，但是计算机存储通常以字节为单位， 一个字节最多只能记录 0-255， 像“字“的unicode值23383 只能通过多个字节存储。而UTF-8 提供的便是如何将一个unicode值转换成多个字节的规则。
#
//示例1
str := "hello world 你好"
// string 转 byte
b := []byte(str)
fmt.Println(b) // [104 101 108 108 111 32 119 111 114 108 100 32 228 189 160 229 165 189]
// []byte转string
str = string(b)
fmt.Println(str) // hello world 你好
//string 转 rune
r := []rune(str)
fmt.Println(r) // [104 101 108 108 111 32 119 111 114 108 100 32 20320 22909]
//rune 转 string
str = string(r)
fmt.Println(str) // hello world 你好
//示例2
var b = "this"
fmt.Println(len(b)) // 占用4 个字节
b = "你好hello"
fmt.Println(len(b)) // 占用11个字节
var c = '国'
fmt.Printf("%v %c %T\n",c,c,c) // 22269(utf-8编码) 国 int32


var m = "hello world"
for i := 0; i < len(m); i++ {
   fmt.Printf("%v(%c) ",m[i],m[i]) //104(h) 101(e) 108(l) 108(l) 111(o) 32( ) 119(w) 111(o) 114(r) 108(l) 100(d)
}

var n = "你好好  我的world"
for _, v := range n {//rune
   fmt.Printf("%v(%c) ",v,v) //20320(你) 22909(好) 22909(好) 32( ) 32() 25105(我) 30340(的) 119(w) 111(o) 114(r) 108(l) 100(d)
}
```

Go中的字符串都是采用`UTF-8`字符集编码。字符串是用一对`双引号`（""）或`反引号`（\`\`）括起来定义，它的类型是`string`。
```go
byte 和 rune 都是字符类型，若多个字符放在一起，就组成了字符串，也就是 string 类型。
var a byte = 'A'
由于 byte 类型能表示的值是有限，只有 2^8=256 个。所以如果你想表示中文的话，只能使用 rune 类型
var b rune = '中'
注意：定义字符使用单引号，如果使用双引号就表示定义的是一个字符串。
```
#### 1.Go中可以使用`+`操作符来`连接`两个字符串。
```go
s := "hello,"
m := " world"
a := s + m
fmt.Printf("%s\n", a)
```
#### 2.Go中字符串是不可变的。
例如下面的代码编译时会报错：cannot assign to s[0]
```go
var s string = "hello"
s[0] = 'c'
```
但如果真的想要修改怎么办呢？下面的代码可以实现：
```go
s := "hello"
c := []byte(s)  // 将字符串 s 转换为 []byte 类型
c[0] = 'c'
s2 := string(c)  // 再转换回 string 类型
fmt.Printf("%s\n", s2)
```
或者采用如下方法来修改字符串：
```go
s := "hello"
s = "c" + s[1:] // 字符串虽不能更改，但可进行切片操作
fmt.Printf("%s\n", s)
```
如果要声明一个多行的字符串怎么办？可以通过`反引号`来声明：
```go
m := `hello
world`
```


## 值类型与引用类型
### 值类型
基本数据类型`int,float,bool,string`以及`数组`和`struct`。
### 引用类型
`指针，切片slice，映射map，通道chan ，接口interface 和函数func 类型`都是引用类型。


## 变量
定义变量：使用关键字 `var` 定义变量，自动初始化为`零值`。如果提供初始化值，可省略变量类型，由 编译器字段推断。
```go
var age int //int类型的零值为0
var price float32 = 1.6

//有初始化可以省略变量类型
var str, num = "abc", 123

func test() {
   //在函数内容可以使用简短声明 := 方式定义变量
   x := 12
   n, s := 0x1234, "Hello, World!"
   println(x, s, n)
}
```

## 常量
必须满足如下`规则`：
```go
1.定义的时候，必须指定值
2.指定的值类型主要有三类： 布尔，数字，字符串， 其中数字类型包含（rune, integer, floating-point, complex), 它们都属于基本数据类型。
3.不能使用 := 
4.常量不能再重新赋值为其他的值
5.不能将函数的返回值赋值给常量
```

```go
// 错误示例1：常量重新赋值，报错
func main() {
   const a = 55 // 允许
   a = 89       // 不允许重新赋值
}
//错误示例2：将函数返回值赋值给常量，因为常量的值需要在编译的时候确定。
func main() {
   fmt.Println("Hello, playground")
   var a = math.Sqrt(4)   // 允许
   const b = math.Sqrt(4) // 不允许
}
```
```go
const filename = "abc.txt"
//const 数值可作为各种类型使用。
const a,b = 3,4
var c int = int(math.Sqrt(a*a + b*b))
```
### iota
```go
package main

import "fmt"

// 批量声明常量时, 如果某一行声明后没有赋值,默认就和上一行一致
const (
   n1 = 100
   n2 // 这一行没有赋值,默认和上一行一致,值仍是100
   n3
)
const (
   d1, d2 = iota + 1, iota + 2 // d1: 1, d2:2
   d3, d4 = iota + 1, iota + 2 // d3: 2, d4:3
)

// iota 是go语言中的常量计数器,只能在常量表达式中使用.
// iota 在const关键字第一次出现时被重置为0, const中每新增一行常量声明将使iota计数加一次

const (
   m1 = iota // 这里iota重置为0
   m2 // 1 在const声明中每新增一行(注意是新增一行,同行的话不会加1),iota的计数加1
   m3 // 2
)

// 使用_跳过某些值
const (
   x1 = iota // 0
   x2 // 1
   _ // 2
   x3 // 3
)

// 插队
const (
   c1 = iota // 0
   c2 = 100 // 中间插入一条记录
   c3 = iota // 2
   c4 // 3
)

// 定义数量级
const (
   _ = iota // 0
   KB = 1 << (10 * iota) // (10 *1) 1向左移10位即2的十次方(1024)
   MB = 1 << (10 * iota) // (10 * 2)
   GB = 1 << (10 * iota)
   TB = 1 << (10 * iota)
   PB = 1 << (10 * iota)
)

func main() {
   fmt.Printf("n1：%d; n2：%d; n3：%d; \n", n1, n2, n3)
   fmt.Printf("m1：%d; m2：%d; m3：%d; \n", m1, m2, m3)
   fmt.Printf("x1：%d; x2：%d; x3：%d; \n", x1, x2, x3)
   fmt.Printf("c1：%d; c2：%d; c3：%d; c3：%d; \n", c1, c2, c3, c4)

   fmt.Printf("d1：%d; d2：%d; d3：%d; d4：%d;\n", d1, d2, d3, d4)
   fmt.Printf("KB：%d; MB：%d; GB：%d; TB：%d; TB：%d;\n", KB, MB, GB, TB, PB)
}
```
### 枚举
```go
func enums(){
   //枚举类型
   const (
      php = iota //表示自增长
      golang
      python
      _
      javascript
   )

   const (
      b = 1 << (10 * iota)
      kb
      mb
      gb
      tb
      pb
   )
   
   fmt.Println(php,javascript,golang,python)
   fmt.Println(b,kb,mb,gb,tb,pb)
}
```
示例切片，接口，数组合并
```go
package main

import (
   "fmt"
)

func main() {
   // Slice Concatenation
   a := []int{1, 2, 3}
   b := []int{7, 12, 60}
   c := append(a, b...)
   fmt.Println(c)

   // Interface Slice Concatenation
   i := []interface{}{1, 2, 3}
   j := []interface{}{"Crosby", "Stills", "Nash", "Young"}
   k := append(i, j...)
   fmt.Println(k)

   // Array Concatenation
   l := [...]int{1, 2, 3}
   m := [...]int{7, 12, 60}
   var n [len(l) + len(m)]int
   copy(n[:], l[:])
   copy(n[len(l):], m[:])
   fmt.Println(n)
}
```
输出：
```go
$ go run main.go
[1 2 3 7 12 60]
[1 2 3 Crosby Stills Nash Young]
[1 2 3 7 12 60]
```

## 获取变量数据类型
```go
// 方法1 使用 fmt.Sprintf()
var v int = 64
fmt.Printf("v的值为: %v, v的类型为: %T\n", v, v)
// 如果想要保存类型到字符串中，可以使用
fieldType1 := fmt.Sprintf("%T", v)
fmt.Println(fieldType1)
// 方法2 使用反射 reflect.TypeOf()
var str string = "hello"
fmt.Println(reflect.TypeOf(str))
```

## nil
nil只能赋值给指针，chan，map，slice，func，interface类型的变量。



















