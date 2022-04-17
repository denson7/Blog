[TOC]
## ES5中的继承
### 1.使用原型链继承

```javascript
function Fu() {
  this.name = 'fu'
}

Fu.prototype.getName = function () {
  console.log(this.name)
}

function Zi() {

}

// 将Zi的原型指向父类Fu
Zi.prototype = new Fu()

const son = new Zi()
son.getName() // 'fu'
```
缺点：
```javascript
// 1.每个实例对引用类型属性的修改都会被其他的实例共享
function Fu() {
  this.names = ['A','B'];
}

function Zi() {
}

Zi.prototype = new Fu()

const zi1 = new Zi()
zi1.names.push('C')
console.log(zi1.names) //[ 'A', 'B', 'C' ]

const zi2 = new Zi()
zi2.names.push('D')
console.log(zi2.names) //[ 'A', 'B', 'C', 'D' ]

// 2.在创建Zi实例的时候，无法向Fu传参。这样就会使Zi实例没法自定义自己的属性
```
### 2.使用构造函数继承

```javascript
function Fu() {
  this.names = ['A', 'B']
}

function Zi() {
  Fu.call(this)
}

const zi1 = new Zi()
zi1.names.push('C')
console.log(zi1.names) //[ 'A', 'B', 'C' ]

const zi2 = new Zi()
zi2.names.push('D')
console.log(zi2.names) //[ 'A', 'B', 'D' ]

# 优点
// 1.解决了每个实例对引用类型属性的修改都会被其他的实例共享的问题
// 2.子类可以向父类传参
function Fu(name) {
  this.name = name
}

function Zi(name) {
  Fu.call(this, name) // 初始化父类的构造函数
}

const a = new Zi('AA');
console.log(a.name); // AA

const b = new Zi('BB');
console.log(b.name); // BB
```
### 3.组合继承

```javascript
function Fu(name) {
  this.name = name
  this.hobby = ['run', 'swimming']
}

function Zi(name, age) {
  Fu.call(this, name) // 构造函数实现传参到父类
  this.age = age
}

Zi.prototype = new Fu()
Zi.prototype.constructor = Zi
const zi1 = new Zi('xiaoming', '18')
zi1.hobby.push('eat')
console.log(zi1.name, zi1.age) //xiaoming 18
console.log(zi1.hobby) //[ 'run', 'swimming', 'eat' ]

const zi2 = new Zi('xiaohong', '20')
zi2.hobby.push('climbing')
console.log(zi2.name, zi2.age) // xiaohong 20
console.log(zi2.hobby) //[ 'run', 'swimming', 'climbing' ]
```
优点：
```
1.解决了每个实例对引用类型属性的修改都会被其他的实例共享的问题
2.子类可以向父类传参
3.可实现父类方法复用
```
缺点:
```
1.需执行两次父类构造函数，第一次是Zi.prototype = new Fu(),第二次是Fu.call(this, name)造成不必要的浪费。
```
### 4.寄生组合继承

```javascript
function inheritPrototype(Fu, Zi){
  //创建父类原型的一个副本,把副本赋值给子类原型
  Zi.prototype = Object.create(Fu.prototype)
  Zi.prototype.constructor = Zi;
}

function Fu(name) {
  this.name = name
}

Fu.prototype.getName = function () {
  console.log(this.name)
}

function Zi(name, age) {
  Fu.call(this, name)
  this.age = age
}

inheritPrototype(Fu, Zi)

const zi1 = new Zi('xiaoming', 17)
console.log(zi1.name) // xiaoming
console.log(zi1.getName()) // xiaoming
console.log(zi1.age) // 17
```
优点：
```
不必为了指定子类型的原型而调用父类型的构造函数。
```



## ES6中的继承
ES6中支持class类，所以继承可以使用extends关键字来实现类的继承。

```javascript
class Point {
  constructor(x, y) {
    this.x = x
    this.y = y
  }

  toString() {
    return this.x + '' + this.y
  }
}

// 使用extends来实现类的继承
class ColorPoint extends Point {
  constructor(x, y, color) {
    super(x, y) //调用父类的构造方法
    this.color = color
  }

  toString() {
    return this.color + ' ' + super.toString() // 调用父类的toString()
  }
}

const colorPoint = new ColorPoint('1', '2', 'red')

console.log(colorPoint.toString())  // red 12
```


