[TOC]
## 结构体
数组、切片和 Map 可以用来表示同一种数据类型的集合，但是当我们要表示`不同数据类型的集合`时就需要用到`结构体`，简单的来说，结构体就是用来定义一些复杂的数据结构。
### `什么是结构体？`
`结构体`，就是由零个或多个任意类型的值聚合成的实体。简单的说：一个结构体（`struct`）就是一个字段的集合。
### `如何定义结构体？`
关键字 `type` 和 `struct` 用来定义结构体：

```go
type StructName struct {
   FieldName1 type1
   FieldName2 type2
   ...
}
```
**示例**
定义一个简单的结构体

```go
package main

import "fmt"
//定义结构体
type Student struct {
   Age     int
   Name    string
}

func main() {
   stu := Student{//一旦定义了结构体，就可以用于变量声明
      Age:     18,
      Name:    "name",
   }
   fmt.Println(stu)

   // 在赋值的时候，字段名可以忽略
   fmt.Println(Student{20, "new name"})
   return
}
```
```go
package main

import "fmt"

//定义结构体
type Employee struct {
   firstName, lastName string
   age, salary         int
}

func main() {
   //创建匿名结构体
   emp3 := struct {
      firstName, lastName string
      age, salary         int
   }{
      firstName: "Nialk",
      lastName:  "Mika",
      age:       31,
      salary:    5000,
   }

   fmt.Println(emp3)
   //访问结构体字段
   fmt.Println("emp3 firstName is",emp3.firstName)

}
```
### 初始化结构体
```go
package main

import "fmt"

type CustomName struct {
   name string
}

func main() {
   n := CustomName{
      name: "john",
   }
   fmt.Println(n, n.name)

   m := CustomName{"Lily"}
   fmt.Println(m, m.name)
   // 结构体切片初始化
   data := []CustomName{{"one"}, {"two"}, {"three"}}
   for _, v := range data {
      fmt.Println(v, v.name)
   }
}
```
```go
package main

import "fmt"

type Person struct {
   name string
   sex  string
   age  int
}

func main() {
   // 方法1. 顺序初始化, 必须全部初始化完整
   var man = Person{"Andy", "man", 18}
   fmt.Println(man) // {Andy man 18}

   // 方法2.部分初始化,必须显示声明key
   woman := Person{sex: "female", age: 19}
   fmt.Println(woman) // { female 19}
   // 索引成员变量,使用"."操作符
   fmt.Println(woman.age) // 19

   // 方法3.使用.操作符初始化
   var zs Person
   zs.name = "zhangsan"
   zs.sex = "man"
   zs.age = 20
   fmt.Println(zs) // {zhangsan man 20}

   // 方法4.使用new关键字，返回指针类型
   var p4 = new(Person)
   p4.name = "Tom"
   p4.sex = "male"
   p4.age = 4
   fmt.Println(p4) // &{Tom male 4}
}
```
```go
type Info struct {
   height int
   age    int
   name   string
}
type User struct {
   name string
   Info
}
u := User{ // 嵌套结构体初始化
   name: "John",
   Info: Info{
      height: 180,
      age: 20,
      name: "info",
   },
}
fmt.Println(u.name) // John
fmt.Println(u.age) // 嵌套结构体字段唯一可以直接通过.访问 20
fmt.Println(u.Info.name) // 相同属性需要指定声明的嵌入结构名称 info

```
#### 设置结构体默认值
```go
package main

import (
   "fmt"
   "reflect"
)

type Person struct {
   name string `default:"john"`
   sex  string
}
// 结构体设置默认值
func defaultStruct(p Person) Person {
   typ := reflect.TypeOf(p)
   // 方法1 通过 returns type
   if p.name == "" {
      f, _ := typ.FieldByName("name")
      p.name = f.Tag.Get("default")
   }
   // 方法2 直接判断赋值
   if p.sex == "" {
      p.sex = "male"
   }
   s := fmt.Sprintf("%s, %s", p.name, p.sex)
   fmt.Println(s)
   return p
}

func main() {
   p := defaultStruct(Person{})
   fmt.Println(p) // {john male}
}
```
### 结构体变量的比较与赋值
```go
package main

import "fmt"

type Person struct {
   name string
   sex  string
   age  int
}

type Student struct {
   p     Person
   id    int
   score []int
}

func test(p Person) {
   p.name = "test"
   p.sex = "female"
   p.age = 22
   fmt.Println("test person ", p)
}

func test2(p *Person) {
   p.name = "test2"
   p.age = 10
   p.sex = "male"
}
func main() {
   // 结构体的比较
   // 结构体只能使用 == 或 !=进行比较, 不能进行>, <, >=, <=比较
   var p1 = Person{"John", "male", 19}
   var p2 = Person{"John", "male", 19}
   var p3 = Person{"John", "male", 20}
   fmt.Println("p1 == p2 ?", p1 == p2) // true
   fmt.Println("p1 == p3 ?", p1 == p3) // false

   // 结构体赋值
   // 只有相同类型(成员变量的类型,个数,顺序一致)的结构体才能赋值
   var tmp Person
   fmt.Println(tmp)
   tmp = p3 // 这里是结构体赋值操作，要求两个结构体类型完全相同，否则报错
   fmt.Println(tmp)
}
```
### 结构体在函数中的传参
结构体有普通结构体变量和指针结构体变量，他们在函数中的传递分别是值传递和引用传递。
```go
package main

import "fmt"

type Person struct {
   name string
   sex  string
   age  int
}

type Student struct {
   p     Person
   id    int
   score []int
}
// 函数的参数传递值传递,要改变原值,需要传递参数的指针
func test(p Person) {
   p.name = "test"
   p.sex = "female"
   p.age = 22
   fmt.Println("test person ", p)
}

func test2(p *Person) {
   p.name = "test2"
   p.age = 10
   p.sex = "male"
}
func main() {
   // 结构体的地址
   // 结构体普通变量的地址等于结构体中首个元素的地址
   fmt.Printf("%p\n", &tmp)      // 0xc0000662d0
   fmt.Printf("%p\n", &tmp.name) // 0xc0000662d0

   // 结构体的传参
   // 结构体的值传参 注意(结构体使用值传递内存消耗大,效率低,几乎不用)
   var temp Person
   fmt.Println(temp) // {  0}
   test(temp)        // 值传递，相当于将实参的值拷贝一份给形参
   fmt.Println(temp) // {  0}

   // 结构体指针变量的初始化
   // 方法1
   var m1 *Person = &Person{"m1", "male", 19}
   fmt.Println(m1)

   // 方法2
   var tp = Person{"m1", "male", 19}
   var m2 *Person
   m2 = &tp
   fmt.Println(m2)

   // 方法3
   m3 := new(Person) // new对结构体初始化,得到的时结构体的地址,即m3是一个Person指针类型
   m3.name = "m3"
   m3.sex = "female"
   m3.age = 12
   fmt.Printf("m3, type=%T\n", m3)

   // 结构体指针变量的值等于结构体中首个元素的地址
   fmt.Printf("m3 = %p\n", m3)             // 0xc00005a4b0
   fmt.Printf("&m3.name = %p\n", &m3.name) // 0xc00005a4b0

   // 结构体指针变量传参
   // 传递结构体变量地址值，引用传递 注意(推荐使用)
   fmt.Println(m3)
   test2(m3)
   fmt.Println(m3)
}

```
### 注意事项
#### 1.通常结构体中一个字段占一行，但类型相同的字段，也可放在同一行。
例如：
```go
type Student struct{
   Age           int
   Name, Address string
}
```
#### 2.一个结构体中的字段名是唯一的。
例如一下代码，出现了两个 Name 字段，是错误的。
```go
type Student struct{
   Name string
   Name string // 错误写法
}

```
#### 3.结构体中的字段如果是小写字母开头，那么其他 package 就无法直接使用该字段。
例如：
```go
// 在包 pk1 中定义 Student 结构体
package pk1

type Student struct{
   Age  int
   name string
}
```
```go
// 在另外一个包 pk2 中调用 Student 结构体
package pk2

func main(){
   stu := Student{}
   stu.Age = 18        //正确
   stu.name = "name"  // 错误，因为`name` 字段为小写字母开头，不对外暴露
}
```
### 结构体嵌套
结构体可以嵌套，但是需要注意的是，如果嵌入的结构体是`本身`，那么只能用`指针`。
```go
package main

import "fmt"

// 结构体嵌套
type person struct {
   name string
   //addr address
   address // 匿名嵌套结构体
}

type company struct {
   name string
   addr address
}

type address struct {
   province string
   city     string
}

func main() {
   // 初始化嵌套的结构体
   p1 := person{
      name: "John",
      address: address{
         province: "Sichuan",
         city:     "chengdu",
      },
   }

   fmt.Println(p1)
   //fmt.Println(p1.addr.city)
   fmt.Println(p1.address.city)
   fmt.Println(p1.city) // 先在自己的结构体中找，找不到再去匿名结构体中找该字段，注意字段要唯一
}

```
```go
package main

import "fmt"

type Tree struct {
   value       int
   left, right *Tree
}

func main() {
   tree := Tree{
      value: 1,
      left: &Tree{
         value: 1,
         left:  nil,
         right: nil,
      },
      right: &Tree{
         value: 2,
         left:  nil,
         right: nil,
      },
   }

   fmt.Printf(">>> %#v\n", tree)
}
```
### 结构体可以进行比较
`前提是结构体中的字段类型是可以比较的。`
```go
package main

import "fmt"

type Tree struct {
   value       int //结构体的属性同样遵循：大写导出，小写私有的原则
   left, right *Tree
}

func main() {
   tree1 := Tree{
      value: 2,
   }

   tree2 := Tree{
      value: 1,
   }

   fmt.Printf(">>> %#v\n", tree1 == tree2)
}
```
### 结构体内嵌匿名成员
声明一个成员对应的数据类型而不指名成员的名字；这类成员就叫匿名成员。
```go
package main

import "fmt"

type Person struct {
   Age  int
   Name string
}

type Student struct {
   Person //匿名成员,这里会继承Person结构体的所有
}

func main() {
   per := Person{
      Age:  18,
      Name: "name",
   }

   stu := Student{Person: per}
   //使用点号操作符.访问结构体的字段
   fmt.Println("stu.Age: ", stu.Age)
   fmt.Println("stu.Name: ", stu.Name)
}
```

## 方法
### 方法的声明
方法（method）的声明和函数很相似, 只不过它必须指定接收者。
```go
// T为接收者，F为方法名
func (t T) F() {
   //
}
```
### 注意事项
#### 1.接收者的类型只能为用关键字 `type `定义的类型，例如`自定义类型`，`结构体`。
#### 2.同一个接收者的方法名`不能重复` 即不支持函数重载。如果是结构体，方法名还不能和`字段名`重复。
####3.值作为接收者无法修改其值，如果需要更改，则需要使用`指针`类型。
示例：
```go
package main

import "fmt"

type Student struct {
   name string
   age  int
}

// 这里定义的方法是 sayHi()，它的接收者为结构体 Student，即接收者写在函数名前的括号里面
// 接收者的类型只能为用关键字 type 定义的类型，例如自定义类型，结构体。
// 简单的来说，定义的结构体好比php中的class类，方法好比类中的方法function，但没有匿名方法，方法重载，构造函数，析构函数等概念
func (s Student) sayHi() {
   fmt.Println("hello world")
}

//接收者可以为指针，需要修改对象成员时，必须要用指针作为接收者
func (s *Student) addAge() {
   s.age += 1
}

func (s Student) printAge() {
   fmt.Println(s.age)
}

func main() {
   s := Student{"xiaoming", 8}
   s.sayHi() // 输出 hello world
   s.printAge() //输出 8
   s.addAge()
   s.printAge() //输出9
}
```
### 为什么有了函数还需要方法？
```go
1.Go 不是纯粹的面向对象编程语言，而且Go不支持类，因此，基于类型(结构体、自定义类型)的方法是一种实现和类相似行为的途径。
2.相同名字的方法可以定义在不同的类型上，而相同名字的函数是不被允许的。
```

### 何时将方法的接收者设置为值类型
1.如果接收者是一个 map，func 或者 chan，使用值类型(因为它们本身就是引用类型)。
2.如果接收者是一个 slice，并且方法不执行 reslice 操作，也不重新分配内存给 slice，使用值类型。
3.如果接收者是一个小的数组或者原生的值类型结构体类型(比如 time.Time 类型)，而且没有可修改的字段和指针，又或者接收者是一个简单地基本类型像是 int 和 string，使用值类型就好了。


### 何时将方法的接收者设置为指针类型
1.如果方法需要修改接收者，接收者必须是指针类型。
2.如果接收者是一个包含了 `sync.Mutex` 或者类似`同步字段`的结构体，接收者必须是指针，这样可以避免拷贝。
3.如果接收者是一个大的结构体或者数组，那么指针类型接收者更有效率。(多大算大呢？假设把接收者的所有元素作为参数传给方法，如果你觉得参数有点多，那么它就是大)。
4.从此方法中并发的调用函数和方法时，接收者可以被修改吗？一个值类型的接收者当方法调用时会创建一份拷贝，所以外部的修改不能作用到这个接收者上。如果修改必须被原始的接收者可见，那么接收者必须是指针类型。
5.如果接收者是一个结构体，数组或者 slice，它们中任意一个元素是指针类型而且可能被修改，建议使用指针类型接收者，这样会增加程序的可读性。

如果还有疑惑，还是不知道该使用哪种接收者，那么建议使用指针接收者。



## 接口
接口类型是一种抽象类型，是`方法的集合`，其他类型实现了这些方法就是实现了这个接口。
### 接口的定义
```go
/* 定义接口 */
type interface_name interface {
   method_name1 [return_type]
   method_name2 [return_type]
   method_name3 [return_type]
   ...
   method_namen [return_type]
}
```
示例：打印不同几何图形的面积和周长
```go
package main

import (
   "fmt"
   "math"
)

type geometry interface {
   area() float32
   perim() float32
}

type rect struct {
   len, wid float32
}

//为结构体rect定义area方法,方法的返回值类型是float32类型
func (r rect) area() float32 {
   return r.len * r.wid
}
//为结构体rect定义perim方法
func (r rect) perim() float32 {
   return 2 * (r.len + r.wid)
}
//rect 实现了geometry的所有方法，我们就是rect实现了geometry的接口

type circle struct {
   radius float32
}

func (c circle) area() float32 {
   return math.Pi * c.radius * c.radius
}

func (c circle) perim() float32 {
   return 2 * math.Pi * c.radius
}

func show(name string, param interface{}) {
   switch param.(type) {
   case geometry:
      // 类型断言
      fmt.Printf("area of %v is %v \n", name, param.(geometry).area())
      fmt.Printf("perim of %v is %v \n", name, param.(geometry).perim())
   default:
      fmt.Println("wrong type!")
   }
}

func main() {
   rec := rect{
      len: 1,
      wid: 2,
   }
   show("rect", rec)

   cir := circle{
      radius: 1,
   }
   show("circle", cir)

   show("test", "test param")
}
```
### 接口实现多态
```go
package main

import "fmt"

// 定义一个接口
type Behavior interface {
   Run() string
   Eat() string
}

// 定义一个结构体 Dog
type Dog struct {
   ID string
   Name string
}
// 定义方法
func (d *Dog) Run() string {
   fmt.Println("Dog is Running")
   return "Dog is Running"
}

func (d *Dog) Eat() string {
   fmt.Println("Dog is Eating")
   return "Dog is Eating"
}

// 定义另一个结构体
type Cat struct {
   ID string
   Name string
}
// 定义方法
func (d *Cat) Run() string {
   fmt.Println("Cat is Running")
   return "Cat is Running"
}

func (d *Cat) Eat() string {
   fmt.Println("Cat is Eating")
   return "Cat is Eating"
}

// 结构体 Dog 和 Cat 均实现(隐式实现)了Run()和Eat()方法，那么也就可以说实现了Behavior这个接口

func action(b Behavior) string  {
   b.Run()
   b.Eat()
   return ""
}

func main() {
   // 使用接口定义变量
   var b Behavior
   // 根据不同的实现方法来实现多态
   //b = new(Cat)
   b = new(Dog)
   b.Run()
   b.Eat()
   //
   c := new(Cat)
   action(c)
}
```
