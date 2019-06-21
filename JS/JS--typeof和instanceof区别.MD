
## typeof和instanceof区别
### typeof
typeof 可以用于检测基本数据类型和引用数据类型。
```
typeof variable
```
返回6种String类型的结果：
```
"undefined" - 如果这个值未定义
"boolean" - 如果这个值是布尔值
"string" - 如果这个值是字符串
"number" - 如果这个值是数值
"object" - 如果这个值是对象或null
"function" - 如果这个值是函数
// 示例
console.log(typeof 'hello'); // "string"
console.log(typeof null); // "object"
console.log(typeof (new Object())); // "object"
console.log(typeof(function(){})); // "function"
```
typeof主要用于`检测基本数据类型`：数值、字符串、布尔值、undefined， 因为typeof用于检测引用类型值时，对于任何Object对象实例（包括null），typeof都返回"object"值。

### instanceof
instanceof 用于判断一个变量是否某个对象的实例。
```
function Person() {
}

function Animal() {
}

var person1 = new Person();
var animal1 = new Animal();
console.log(person1 instanceof Person); // true
console.log(animal1 instanceof Person); // false
console.log(animal1 instanceof Object); // true 因为所有引用类型的值都是Object的实例
console.log(1 instanceof Person); //false， 1是基本类型，基本类型不是对象，所以返回false

var oStr = new String("hello world");
console.log(typeof (oStr)); // object
console.log(oStr instanceof String);
console.log(oStr instanceof Object);

// 判断 foo 是否是 Foo 类的实例
function Foo() {
}

var foo = new Foo();
console.log(foo instanceof Foo);

// instanceof 在继承中关系中的用法
console.log('instanceof 在继承中关系中的用法');

function Aoo() {
}

function Foo() {
}

Foo.prototype = new Aoo();
var fo = new Foo();
console.log(fo instanceof Foo);
console.log(fo instanceof Aoo)
```
