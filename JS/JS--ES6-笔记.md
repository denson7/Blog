[TOC]
## ES6的模块化
import 和 export

## var,let,const区别

```
let 是块作用域（大括号内就是块作用域），且不能重复声明。
const 声明必须赋值，且不能修改。
```
## 变量的解构赋值
### 数组的解构

```javascript
#old
let a = 1;
let b = 2;
let c = 3;

#new
let [a, b, c] = [1, 2, 3]
//只要等号两边的模式相同，左边的变量就会被赋予对应的值。

#示例
let [foo, [[bar], baz]] = [1, [[2], 3]];
foo // 1
bar // 2
baz // 3

let [ , , third] = ["foo", "bar", "baz"];
third // "baz"

let [x, , y] = [1, 2, 3];
x // 1
y // 3

let [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]

let [x, y, ...z] = ['a'];
x // "a"
y // undefined,如果解构不成功就是undefined
z // []

#数组解构设置默认值
let[foo=true]=[];
foo// true

let[x,y='b']=['a'];// x='a', y='b'
let[x,y='b']=['a',undefined];// x='a', y='b'
```
### 对象的解构

```javascript
#与对象的解构区别：数组是按次序排列的，变量的取值由它的位置决定；而对象的属性没有次序，变量必须与属性同名，才能取到正确的值。
let { foo, bar } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"

let{baz}={foo:"aaa",bar:"bbb"};
baz// undefined 在对象中找不到与变量相同的属性名，所以undefined
#如果要重新定义变量，必须写成如下形式：
let{foo:baz}={foo:'aaa',bar:'bbb'};
baz// "aaa"
//foo是匹配的模式，baz才是变量。真正被赋值的是变量baz，而不是模式foo
letobj={first:'hello',last:'world'};
let{first:f,last:l}=obj;
f// 'hello'
l// 'world'

#对象解构设置默认值
var{x,y=5}={x:1};
x// 1
y// 5

var{x:y=3}={};
y// 3

var{x:y=3}={x:5};y// 5
var{message:msg='Something went wrong'}={};
msg// "Something went wrong"
```
### 字符串的解构
字符串也可以解构赋值。这是因为此时，字符串被转换成了一个类似数组的对象。它有一个length属性。

```javascript
const[a,b,c,d,e]='hello';
a// "h"
b// "e"
c// "l"
d// "l"
e// "o"
let{length:len}='hello';
len// 5
```
### 解构的用途
#### 1.交换变量的值

```javascript
letx=1;
lety=2;

[x,y]=[y,x];
```
#### 2.从函数返回多个值

```javascript
//函数只能返回一个值，如果要返回多个值，只能将它们放在数组或对象里返回,使用解构赋值，取值很方便
function example() {
  return [1, 2, 3];
}
let [a, b, c] = example();

// 返回一个对象
function example() {
  return {
    foo: 1,
    bar: 2
  };
}
let { foo, bar } = example();
```
#### 3.函数参数的定义

```javascript
//解构赋值可以方便地将一组参数与变量名对应起来。
// 参数是一组有次序的值
function f([x, y, z]) { ... }
f([1, 2, 3]);

// 参数是一组无次序的值
function f({x, y, z}) { ... }
f({z: 3, y: 2, x: 1});
```
#### 4.提取JSON数据

```javascript
let jsonData = {
  id: 42,
  status: "OK",
  data: [867, 5309]
};

let { id, status, data: number } = jsonData;

console.log(id, status, number);
// 42, "OK", [867, 5309]
```
#### 5.函数参数的默认值

```javascript
jQuery.ajax = function (url, {
  async = true,
  beforeSend = function () {},
  cache = true,
  complete = function () {},
  crossDomain = false,
  global = true,
  // ... more config
} = {}) {
  // ... do stuff
};
```
#### 6.遍历map结构

```javascript
const map = new Map();
map.set('first', 'hello');
map.set('second', 'world');

for (let [key, value] of map) {  //只获取键 let [key] of map；只获取值let [,value] of map
  console.log(key + " is " + value);
}
// first is hello
// second is world
```
#### 7.输入模块的指定方法

```javascript
const { SourceMapConsumer, SourceNode } = require("source-map");
```

## 函数的扩展
### 默认参数

```javascript
#ES6之前
functionlog(x,y){  
  y=y||'World'; //判断参数y有没有赋值，没有，则设默认值为World
  console.log(x,y);
}
#ES6之后
function log(x, y = 'World') {
  console.log(x, y);
}

//注意，以下写法错误，因为参数变量是默认声明的，所以不能用let或const再次声明。
function foo(x = 5) {
  let x = 1; // error
  const x = 2; // error
}
```
### 与解构赋值默认值结合
参数默认值可以与解构赋值的默认值，结合起来使用。

```javascript
function foo({x, y = 5}) {
  console.log(x, y);
}

foo({}) // undefined 5
foo({x: 1}) // 1 5
foo({x: 1, y: 2}) // 1 2
foo() // TypeError: Cannot read property 'x' of undefined
//报错原因：只使用了对象的解构赋值默认值，没有使用函数参数的默认值。只有当函数foo的参数是一个对象时，变量x和y才会通过解构赋值生成。如果函数foo调用时没提供参数，变量x和y就不会生成，从而报错。
//解决办法：通过这是参数默认值，可以解决，如下代码
function foo({x, y = 5} = {}) { //设置参数默认值是一个空对象
  console.log(x, y);
}

foo() // undefined 5

#练习
// 写法一
function m1({x = 0, y = 0} = {}) {
  return [x, y];
}
// 写法二
function m2({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}
//区别：
//写法一 函数参数的默认值是空对象，但是设置了对象解构赋值的默认值；
//写法二 函数参数的默认值是一个有具体属性的对象，但是没有设置对象解构赋值的默认值.
// 函数没有参数的情况
m1() // [0, 0]
m2() // [0, 0]

// x 和 y 都有值的情况
m1({x: 3, y: 8}) // [3, 8]
m2({x: 3, y: 8}) // [3, 8]

// x 有值，y 无值的情况
m1({x: 3}) // [3, 0]
m2({x: 3}) // [3, undefined]

// x 和 y 都无值的情况
m1({}) // [0, 0];
m2({}) // [undefined, undefined]

m1({z: 3}) // [0, 0]
m2({z: 3}) // [undefined, undefined]
```
### rest参数
ES6 引入 rest 参数（形式为`...变量名`），用于获取函数的多余参数，这样就不需要使用`arguments`对象了。rest 参数搭配的变量是一个`数组`，该变量将多余的参数放入数组中。

```javascript
function push(array, ...items) {
  items.forEach(function(item) {
    array.push(item);
    console.log(item);
  });
}

var a = [];
push(a, 1, 2, 3)
#注意：rest 参数之后不能再有其他参数（即只能是最后一个参数），否则会报错。函数的length属性，不包括 rest 参数。
```
### name属性
函数的`name`属性，返回该函数的函数名。

```javascript
function foo() {}
foo.name //"foo"

var f = function () {};
f.name //"f"
```
### 箭头函数

```javascript
const obj = {
  id: 2,
  say: function () {
    setTimeout(function () {
      console.log("id", this.id); // undefined
    }, 50);
  },
  sayWithThis: function () {
    let that = this;
    setTimeout(function () {
      console.log("this id", that.id); // 2
    }, 100);
  },
  sayWithArrow: function () {
    setTimeout(() => {
      console.log("arrow id", this.id); // 2
    }, 150);
  },
  sayWithGlobal: () => {
    setTimeout(() => {
      console.log("global id", this.id); // undefined
    }, 200);
  },
};

obj.say(); // id undefined
obj.sayWithThis(); // this id 2
obj.sayWithArrow(); // arrow id 2
obj.sayWithGlobal(); // global id undefined

```
```javascript
var f = v => v;

// 等同于
var f = function (v) {
  return v;
};

#如果不需要参数或需要多个参数，就使用一个圆括号代表参数部分
var f = () => 5;
// 等同于
var f = function () { return 5 };

var sum = (num1, num2) => num1 + num2;
// 等同于
var sum = function(num1, num2) {
  return num1 + num2;
};
#如果箭头函数直接返回一个对象，必须在对象外面加上括号，否则会报错。因为大括号会被解释为代码块，所以要返回一个对象，需要在外层加圆括号
// 报错
let getTempItem = id => { id: id, name: "Temp" };

// 不报错
let getTempItem = id => ({ id: id, name: "Temp" });

#如果箭头函数的代码块部分多于一条语句，就要使用大括号将它们括起来，并且使用return语句返回。
var sum = (num1, num2) => { return num1 + num2; }

#箭头函数可以与变量解构结合
const full = ({ first, last }) => first + ' ' + last;
// 等同于
function full(person) {
  return person.first + ' ' + person.last;
}

const headAndTail = (head, ...tail) => [head, tail];
headAndTail(1, 2, 3, 4, 5)
// [1,[2,3,4,5]]

#简化代码
var result = values.sort((a, b) => a - b);
//等同于
var result = values.sort(function (a, b) {
  return a - b;
})
```
#### 箭头函数与普通函数的区别
1.箭头函数比普通函数更加简洁
```
如果没有参数，就直接写一个空括号即可
如果只有一个参数，可以省去参数的括号
如果有多个参数，用逗号分割
如果函数体的返回值只有一句，可以省略大括号
如果函数体不需要返回值，且只有一句话，可以给这个语句前面加一个void关键字。最常见的就是调用一个函数：let fn = () => void doesNotReturn();
```
2.箭头函数没有自己的this 
箭头函数不会创建自己的this， 所以它没有自己的this，它只会在自己作用域的上一层继承this。所以箭头函数中this的指向在它在定义时已经确定了，之后不会改变。所以它所谓的this是捕获其所在上下⽂的 this 值。
3.箭头函数继承来的this指向永远不会改变
```javascript
var id = 'GLOBAL';
var obj = {
  id: 'OBJ',
  a: function(){
    console.log(this.id);
  },
  b: () => {
    console.log(this.id);
  }
};
obj.a();    // 'OBJ'
obj.b();    // 'GLOBAL'
new obj.a() // 报错，不能作为构造函数
new obj.b() 
// 说明：对象obj的方法b是使用箭头函数定义的，这个函数中的this就永远指向它定义时所处的全局执行环境中的this，即便这个函数是作为对象obj的方法调用，this依旧指向Window对象。需注意的是，定义对象的大括号{}是无法形成一个单独的执行环境的，它依旧是处于全局执行环境中。
```
4.call()、apply()、bind()等方法不能改变箭头函数中this的指向
```javascript
var id = 'Global';
let fun1 = () => {
    console.log(this.id)
};
fun1();                     // 'Global'
fun1.call({id: 'Obj'});     // 'Global'
fun1.apply({id: 'Obj'});    // 'Global'
fun1.bind({id: 'Obj'})();   // 'Global'
```
5.箭头函数不能作为构造函数使用 
由于箭头函数时没有自己的this的，且this指向外层的执行环境，且不能改变指向，所以不能当做构造函数使用
6.箭头函数没有自己的arguments 
箭头函数没有自己的arguments对象。在箭头函数中访问arguments实际上获得的是它外层函数的arguments值
7.箭头函数没有prototype
8.箭头函数不能用作Generator函数，不能使用yeild关键字

## 数组的扩展
### 扩展运算符...
扩展运算符可以展开数组，常用于将数组转为函数的参数了。

```javascript
console.log(1, ...[2, 3, 4], 5)
// 1 2 3 4 5
//常用于函数中
function push(array, ...items) {
  array.push(...items); //扩展运算符 ... 将一个数组，变为参数序列
}

function add(x, y) {
  return x + y;
}

const numbers = [4, 38];
add(...numbers) // 42  

console.log(...(-3 >0 ? ["a"] : [1,2,3])) //也可用于表达式
//1 2 3
```
### 扩展运算符的应用
#### 复制数组

```javascript
//数组是复合的数据类型，直接复制的话，只是复制了指向底层数据结构的指针，而不是克隆一个全新的数组。
#复制 浅拷贝，即是对原数据的引用，修改原数据会影响新数据
const a1 = [1, 2];
const a2 = a1;

a2[0] = 2;
a1 // [2, 2]
#ES5写法
const a1 = [1, 2];
const a2 = a1.concat();

a2[0] = 2;
a1 // [1, 2]

#ES6写法
consta1=[1,2];
// 写法一
consta2=[...a1];
// 写法二
const[...a2]=a1;
```
#### 合并数组

```javascript
const arr1 = ['a', 'b'];
const arr2 = ['c'];
const arr3 = ['d', 'e'];

#ES5 的合并数组
arr1.concat(arr2, arr3);

#ES6 的合并数组
[...arr1, ...arr2, ...arr3]
```
#### 与解构赋值结合

```javascript
list = [1,2,34,'a','b']
#ES5 写法
a = list[0], rest = list.slice(1)
#ES6 写法
[a, ...rest] = list
```
#### 字符串

```javascript
[...'hello']
["h", "e", "l", "l", "o"]
```


























##修饰器
###类的修饰
```
@testable

class MyTestableClass {

  // ...

}


function testable(target) { //参数target就是被装饰的那个类本身，这里指的是MyTestableClass类

  target.isTestable = true;

}


MyTestableClass.isTestable // true

```
`说明：`上面代码中，`@testable`就是一个修饰器。它修改了`MyTestableClass`这个类的行为，为它加上了静态属性`isTestable`。`testable函数`的参数`target`是`MyTestableClass`类本身。
本质上等价于：
```
#修饰器是一个对类进行处理的函数。修饰器函数的第一个参数，就是所要修饰的目标类
@decorator 

class A {}


// 等同于


class A {}

A = decorator(A) || A;

```
###方法的修饰
修饰器不仅可以修饰类，还可以修饰类的属性或方法。
```
class Person {

  @readonly //修饰Person类的name方法

  name() { return `${this.first} ${this.last}` }

}
#readonly
function readonly(target, name, descriptor){
  // descriptor对象原来的值如下
  // {
  //   value: specifiedFunction,
  //   enumerable: false,
  //   configurable: true,
  //   writable: true
  // };
  descriptor.writable = false;
  return descriptor;
}
readonly(Person.prototype, 'name', descriptor);

// 类似于

Object.defineProperty(Person.prototype, 'name', descriptor);

```
`备注：`如果同一个方法有多个修饰器，会像剥洋葱一样，先从外到内进入，然后由内向外执行。
###修饰器不能用于函数
修饰器只能用于类和类的方法，不能用于函数，因为存在函数提升。


