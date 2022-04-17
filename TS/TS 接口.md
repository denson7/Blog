[TOC]
## 接口 interface
```javascript
// 接口：使用接口（Interfaces）来定义对象的类型
// 属性完全匹配，属性不能多也不能少
interface Person1 {
    name: string;
    age: number;
}

let person1: Person1 = {
    name: 'xiaoming',
    age: 25
};
//可选属性
interface Person2 {
    name: string;
    age?: number; // 属性? 表示该属性为可选属性
}

let person2: Person2 = {
    name: 'Tom'
};

// 任意属性
interface Person3 {
    name: string;
    age?: number;
    [propName: string]: any; // 允许任意属性
}

let person3: Person3 = {
    name: 'Tom',
    age: 25,
    gender: 'male',
    height: 175,
};

// 只读属性
interface Person4 {
    readonly id: number; // 使用readonly 来修饰只读属性
    name: string;
    age?: number;
    [propName: string]: any; // 额外属性的检查
}

let person4: Person4 = {
    id: 89757,
    name: 'Tom',
    gender: 'male'
};

// person4.id = 9527; // 报错，id为只读属性，不能再进行赋值
```
### 可选属性
接口的属性可以不是必须的。
```javascript
interface Person {
    name: string
    age?: number // 可选属性
}

let man: Person = {
    name: 'James'
}
```
### 只读属性
```javascript
// 只读属性
interface Point {
    readonly x: number
    readonly y: number
}

let p1: Point = {x: 10, y: 20};
// p1.x = 3; // p1的x属性是只读属性,不能重新赋值

let a: number[] = [1,2,3,4];
let ro : ReadonlyArray<number> = a;

// ro.push(2);  // ro是只读的数组,所以不能push

// 强制赋值ro 给 a
a = ro as number[]; // 使用类型断言
```
### 任意属性
```javascript
// 用任意的字符串索引，使其可以得到任意的结果
interface Person {
    name: string
    age: number
    [x: string]: any
    // 除了 name 和 age 必须一致以外，其他属性可以随意定义数量不限,注意：其他属性的类型必须是任意属性类型的子集
}

let man: Person = {
    name: 'James',
    age: 30,
    height: '180cm'
}

```
### 对象类型接口
```javascript
interface List {
    readonly id: number
    name: string
    age?: number
}

interface Result {
    data: List[]
}

function render (result: Result) {
    console.log(JSON.stringify(result))
}

let result = {
    data: [
        { id: 1, name: 'A', sex: 'male' },
        { id: 2, name: 'B' }
    ]
}

render(result) // 不会被检测出错误
render({
    data: [
        { id: 1, name: 'A', sex: 'male' }, // 将对象字面当做参数传给了 render 函数这里会报错
        { id: 2, name: 'B' }
    ]
})
// Error: Object literal may only specify known properties, and 'sex' does not exist in type 'List'.

// 通过类型断言规避这个问题
// 方法1: 使用as关键字
render({
    data: [
        { id: 1, name: 'A', sex: 'male'},
        { id: 2, name: 'B' }
    ]
} as Result)
// 方法2：使用 <> 符号
render(<Result>{
    data: [
        { id: 1, name: 'A', sex: 'male'},
        { id: 2, name: 'B' }
    ]
})
```
### 函数类型接口
```javascript
// 方法1：变量声明一个函数类型
let add1: (x: number, y: number) => number;
// 方法2接口声明函数类型
interface Add2 {
    (x: number, y: number): number
}
let add2: Add2 = (a, b) => a + b;

// 方法3：使用类型别名 type 关键字
type Add3 = (x: number, y: number) => number;

let add3: Add3 = (a, b) => a + b;
```
### 类型别名
```javascript
type Name = string //定义一个string 类型的别名为Name
type NameFn = () => string // 定义一个函数的别名为NameFn
```


