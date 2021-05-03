[TOC]
## Golang 数据类型转换
Go是`强类型`的语言，`没有`隐式的类型提升和转换。
### 基础数据类型转换
```go
func test() {
   // int64 -> float64
   var a1 int64 = 10
   var b1 float64
   b1 = float64(a1)
   fmt.Printf("%T:%v\n", b1, b1) // float64:10

   // float64 -> int
   var a2, b2 int = 3, 4
   var c2 int
   // 强制类型转换,math.Sqrt()函数的变量和返回值都是float64,所以这里需要强制转换
   c2 = int(math.Sqrt(float64(a2*a2 + b2*b2)))
   fmt.Printf("%T:%v\n", c2, c2) // int:5

   // interface{} -> int, string, float
   var a3 interface{} = 3
   var c3 int
   c3 = a3.(int)                 // 类型断言
   fmt.Printf("%T:%v\n", c3, c3) // int:3

   var a4 interface{} = 3.23
   c4 := a4.(float64)            // 类型断言
   fmt.Printf("%T:%v\n", c4, c4) // float64:3.23

   var a5 interface{} = "aa"
   c5 := a5.(string)             // 类型断言
   fmt.Printf("%T:%v\n", c5, c5) // string:aa
}
```
### interface转结构体
```go
type User struct {
   Name string
}

func convertStruct()  {
   u := User{
      Name: "John",
   }
   a6 := TestStruct(u)
   fmt.Printf("%T:%v\n", a6, a6) // main.User:{John}
}

// interface -> struct
func TestStruct(data interface{}) User  {
   //方法1：使用断言，强制转换
   user := data.(User)
   //方法2：使用json序列化
   //resByte, _ := json.Marshal(data)
   //var user User
   //_ = json.Unmarshal(resByte, &user)
   //fmt.Printf("%T:%v\n", user, user) // main.User:{John}
   return user
}
```
### interface转切片
```go
/*
list = [
   {name-test1 email1@qq.com 成都 170 2010-02-24 18:00:00 +0800 CST}
   {name-test2 email2@qq.com 深圳 180 2000-03-05 10:54:18 +0800 CST}
]
*/
func interfaceToSlice(list interface{}) {
   type List struct {
      Name      string    `json:"name" csv:"Name"`
      Email     string    `json:"email" csv:"Email"`
      Address   string    `json:"address" csv:"Address"`
      CreatedAt time.Time `json:"created_at" csv:"CreatedAt"`
   }
   var data []List
   // 通过反射将interface转换为切片
   if reflect.TypeOf(list).Kind() == reflect.Slice {
      s := reflect.ValueOf(list)
      fmt.Println(s)
      for i := 0; i < s.Len(); i++ {
         ele := s.Index(i)
         data = append(data, ele.Interface().(List))
      }
   }
   fmt.Println(data)
}
```
## Golang 常用数据类型比较
### 整型
可比较
### 布尔类型
可比较
### 浮点数
可比较
### 复数
可比较
### 字符串
可比较
```go
//字符串比较
//方法1：使用 == 运算符，逐个比较且区分大小写，相等返回true，不相等返回false
fmt.Println("你好" == "你好") //true
fmt.Println("sa" == "sa") //true
fmt.Println("SA" == "Sa") //false

//方法2：使用 strings.EqualFold() 不区分大小写，相等返回true，不相等返回false
fmt.Println(strings.EqualFold("你好", "你好"))       //true
fmt.Println(strings.EqualFold("HELLO", "hello")) //true

//方法3：使用strings.Compare() 效率比==更高，相等返回0，不相等返回1
fmt.Println(strings.Compare("Hello", "hello")) //1
fmt.Println(strings.Compare("AB", "AB")) //0

```
### 指针
可比较。条件：两个指针指向同一个变量，则这两个指针相等，或者两个指针同为nil，它们也相等。指针值可以与nil比较。
```go
var num1, num2 int
num1 = 8
num2 = 8

pt1 := &num1
pt2 := &num1
pt3 := &num2

//定义一个空指针
var pt4 *int

//只有指向同一个变量，两个指针才相等
//输出：true false
fmt.Printf("%v %v\n", pt1 == pt2, pt1 == pt3)

//指针可以与nil直接比较
//输出：true false
fmt.Printf("%v %v\n", pt4 == nil, pt1 == nil)//true false

```
### 数组
可比较。条件：如果数组中的元素类型是可比的，则数组也是可比较的。如果数组中对应的元素都相等，那么两个数组是相等的。数组不能与nil比较
```go
a1 := [3]int{1, 2, 3}
a2 := [3]int{1, 2, 3}
a3 := [3]int{2, 1, 3}

//元素顺序必须一样
//输出：true false
fmt.Printf("%v %v\n", a1 == a2, a2 == a3)
//invalid operation: a3 == nil (mismatched types [3]int and nil)
fmt.Printf("%v\n", a3 == nil)
```
### 结构体
可比较。条件：如果struct中所有的字段都是可比较的，那么两个struct是可比较的。如果struct对应的非空白字段相等，则它们相等。struct不能与nil比较。
```go
type person struct {
   name string
   age  int
}

p1 := person{name: "luna"}
p2 := person{"ele", 0}
p3 := person{"luna", 0}

//输出：false true
fmt.Printf("%v %v\n", p1 == p2, p1 == p3)

var p4 person
var p5 person

//输出：true
fmt.Printf("%v\n", p4 == p5)
```
### 通道chan
可比较。条件：如果两个通道是由同一个make创建的，或者两个通道值都为nil，那么它们是相等的。
```go
ch1 := make(chan int)
ch2 := make(chan int)

var ch3 chan int
ch3 = ch1

var ch4 chan int
var ch5 chan int
var ch6 chan string

//同一个make创建的通道才相等
//输出：false true
fmt.Printf("%v %v\n", ch1 == ch2, ch3 == ch1)

//通道值可与nil比较
//输出：true true
fmt.Printf("%v %v\n", ch4 == ch5, ch5 == ch1)

//两个不同类型的通道，即使都是空值，也不能比较
//invalid operation: ch5 == ch6 (mismatched types chan int and chan string)
fmt.Printf("%v\n", ch5 == ch6)
```
### 接口值
可比较
### map
不可比较，只能与nil比较
### slice
不可比较
```go
// slice/struct/map 都可以用 reflect.DeepEqual() 来判断是否相等
type S struct {
   s string
}

s1 := S{s: "hello"}
s2 := S{s: "hello"}
if reflect.DeepEqual(s1, s2) {
   fmt.Println(s1, "==", s2)
}

a :=[] int {1, 2, 3 }
b :=[] int {1, 2, 4}
c :=[] int {1, 2, 3}
println(reflect.DeepEqual(a, b))   //false
println(reflect.DeepEqual(a, c))   //true

m1 := map[int]string{1: "a", 2: "b"}
m2 := map[int]string{1: "a", 2: "b"}
if reflect.DeepEqual(m1, m2) {
   fmt.Println(m1, "==", m2)
}
```

