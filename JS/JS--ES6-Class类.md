[TOC]
## 类

### ES5如何写一个类
```javascript
// ES5中写一个类
// 一般是函数名大写
function Person(name, age) {
  // 给类定义属性
  this.name = name
  this.age = age
}
// 静态方法
Person.log = function (info) {
  // 静态方法的 this 指向类  所以无法获取 this.name
  console.log(info)
}
Person.log('这是一个test ')

// 给这个类添加方法一般是挂载在原型链上
Person.prototype.running = function () {
  console.log(this.name, this.age, "running")
}

// 实例化对象使用需要new
const p = new Person("john", 17)
console.log(p.name, p.age)
p.running()


// ES5 中类的继承
// 父类
function Animal(name) {
  this.name = name
}
Animal.prototype.showName = function () {
  console.log('名字是:' + this.name)
}

// 子类
function Dog(name, color) {
  // 继承属性
  Animal.call(this, name)
  this.color = color
}

// 继承父类方法
Dog.prototype = new Animal()
Dog.prototype.constuctor = Dog

let d1 = new Dog('wangwang', 'yellow')
console.log(d1)
d1.showName()

```
### 类的定义和创建实例

```javascript
//定义一个Parent类
class Parent{
  //类的构造方法
  constructor(name='demo'){//默认属性
    this.name= name;
  }
}

//生成一个实例
let p = new Parent();
console.log(p); //{name: "demo"}
let p1 = new Parent('test') // 'p1'就是构造函数name属性 , 覆盖构造函数的name属性值

```
### 类的继承

```javascript
// 父类
class Parent{ //定义一个类
  constructor(name='xiaxaioxian'){
    this.name= name;
  }
}
// 子类继承父类，并覆盖父类的构造函数的方法
class Child extends Parent{
  constructor(name = 'child'){ // 子类重写name属性值
    super(name); // 子类向父类修改 super一定放第一行
    this.age= 27;//给子类新增属性
  }
}

console.log('继承',new Child('hello')) // 带参数覆盖默认值  继承{name: "hello", age: 27}

```
### getter和setter

```javascript
// getter和setter
class Parent{
  constructor(name='test'){
    this.name= name
  }

  //这里是longName属性，而不是方法，
  get longName(){ // 属性
    return 'hello' + this.name
  }
  // 这里是设置属性
  set longName(value){
    this.name = value
  }
}

let p = new Parent();
console.log('getter',p.longName)  // getter hellotest

p.longName = 'world';
console.log('setter',p.longName)  // setter helloworld

```
### 静态方法与静态属性

```javascript
class Parent{
  constructor(name='test'){
    this.name= name
  }

  // 关键字static定义一个静态方法:可以直接通过类调用，而不需要去new一个实例
  static run(){
    console.log('run')
  }
}
//定义静态属性，直接在类上定义
Parent.gender='male'

// 直接通过类调用
Parent.run(); // run
Parent.gender;//male

```
## 示例
### 使用ES6写一个列表

```javascript
class List{
  /**
   * 初始化
   */
  constructor(){//守则默认属性
    this.listSize= 0;
    this.pos= 0;
    this.dataSource= [];
  }
  /**
   * 给列表添加元素
   */
  append(element) {
    this.listSize++;
    this.dataSource.push(element);
  }

  /**
   * @param element 如果传入的是对象，需要判断是否是对象以及两个对象是否相等
   * @returns {number} 如果找到，返回位置，否则-1
   */
  find(element) {
    for (var i = 0; i < this.dataSource.length; i++) {
      if (this.dataSource[i] === element) {
        return i;
      }
    }
    return -1;
  }

  /**
   * 返回列表元素的个数
   * @returns {number}
   */
  length() {
    return this.listSize;
  }

  /**
   * 删除元素成功，元素个数-1
   * @param element
   * @returns {boolean}
   */
  remove(element) {
    var removeIndex = this.find(element);
    if (removeIndex !== -1) {
      this.dataSource.splice(removeIndex, 1);
      this.listSize--;
      return true;
    }
    return false;
  }

  /**
   * 返回要展示的列表
   * @returns {string}
   */
  toString() {
    return this.dataSource.toString();
  }

  /**
   * 插入某个元素
   * @param element 要插入的元素
   * @param afterElement 列表中的元素之后
   * @returns {boolean}
   */
  insert(element, afterElement) {
    var insertIndex = this.find(afterElement);
    if (insertIndex !== -1) {
      this.dataSource.splice(insertIndex + 1, 0, element);
      this.listSize++;
      return true;
    }
    return false;
  }

  /**
   * 清空列表中的所有元素
   */
  clear() {
    delete this.dataSource;
    this.dataSource = [];
    this.listSize = this.pos = 0;
  }

  /**
   * 将列表的当前位置移动到第一个元素
   */
  front() {
    this.pos = 0;
  }

  /**
   * 将列表的当前位置移动到最后一个元素
   */
  end() {
    this.pos = this.listSize - 1;
  }

  /**
   * 返回当前位置的元素
   * @returns {*}
   */
  getElement() {
    return this.dataSource[this.pos];
  }

  /**
   * 将当前位置向前移动一位
   */
  prev() {
    if (this.pos > 0) {
      --this.pos;
    }
  }

  /**
   * 将当前位置向后移动一位
   */
  next() {
    if (this.pos < this.listSize-1) {
      ++this.pos;
    }
  }

  /**
   * 返回列表的当前位置
   * @returns {number|*}
   */
  currPos() {
    return this.pos;
  }

  /**
   * 移动到指定位置
   * @param position
   */
  moveTo(position) {
    this.pos = position;
  }

  /**
   * 判断是否有后一位
   * @returns {boolean}
   */
  hasNext() {
    return this.pos < this.listSize;
  }

  /**
   * 判断是否有前一位
   * @returns {boolean}
   */
  hasPrev() {
    return this.pos >= 0;
  }
}

var names = new List();
names.append("Clayton");
names.append("Raymond");
names.append("Cynthia");
names.append("Jennifer");
names.append("Bryan");
names.append("Danny");

names.front();
console.log(names.getElement()); // 显示 Clayton

names.next();
console.log(names.getElement()); // 显示 Raymond


names.next();
names.next();
names.prev();
console.log(names.getElement()); // 显示 Cynthia

```
### 使用ES6写一个栈

```javascript
class Stack {
  constructor() {
    this.dataStore = [];
    this.top = 0;
  }

  /**
   * 向栈中压入一个新元素
   */
  push(element) {
    this.dataStore[this.top++] = element;
  }

  /**
   * 返回数组的栈顶元素：
   */
  peek() {
    return this.dataStore[this.top - 1];
  }

  pop() {
    return this.dataStore[--this.top];
  }

  clear() {
    this.top = 0;
  }

  length() {
    return this.top;
  }
}

/**
 * 将数字转化二进制或八进制数字
 */
function mulBase(num, base) {
  var s = new Stack();
  do {
    s.push(num % base);
    num = Math.floor(num /= base);
  } while (num > 0);
  var converted = "";
  while (s.length() > 0) {
    converted += s.pop();
  }
  return converted;
}


var s = new Stack();
s.push("David");
s.push("Raymond");
s.push("Bryan");
console.log("length: " + s.length());
console.log(s.peek());
var popped = s.pop();
console.log("The popped element is: " + popped);
console.log(s.peek());
s.push("Cynthia");
console.log(s.peek());
s.clear();
console.log("length: " + s.length());
console.log(s.peek()); //空栈调用 peek() 方法，结果为 undefined
s.push("Clayton");
console.log(s.peek());
// 将数字转化二进制
var num = 32;
var base = 2;
var newNum = mulBase(num, base);
console.log(num + " converted to base " + base + " is " + newNum);
// 将数字转化八进制
num = 125;
base = 8;
var newNum = mulBase(num, base);
console.log(num + " converted to base " + base + " is " + newNum);


/**
 * 判断给定字符串是否是回文
 */
function isPalindrome(word) {
  var s = new Stack();
  for (var i = 0; i < word.length; ++i) {
    s.push(word[i]);
  }
  var rword = "";
  while (s.length() > 0) {
    rword += s.pop();
  }
  return word == rword ? true : false;
}

var word = "hello";
if (isPalindrome(word)) {
  console.log(word + " is a palindrome.");
} else {
  console.log(word + " is not a palindrome.");
}

word = "racecar"
if (isPalindrome(word)) {
  console.log(word + " is a palindrome.");
} else {
  console.log(word + " is not a palindrome.");
}

```
### 使用ES6写一个队列

```javascript
class Queue {
  constructor() {
    this.dataStore = [];
  }

  /**
   * 向队尾添加一个元素
   */
  enqueue(element) {
    this.dataStore.push(element);
  }

  /**
   * 删除队首的元素
   */
  dequeue() {
    return this.dataStore.shift();
  }

  /**
   * 读取队首的元素
   */
  front() {
    return this.dataStore[0];
  }

  /**
   * 读取队尾的元素
   */
  back() {
    return this.dataStore[this.dataStore.length - 1];
  }

  /**
   *  toString() 方法显示队列内的所有元素
   */
  toString() {
    var retStr = "";
    for (var i = 0; i < this.dataStore.length; ++i) {
      retStr += this.dataStore[i] + "\n";
    }
    return retStr;
  }

  /**
   * 判断队列是否为空
   */
  empty() {
    if (this.dataStore.length == 0) {
      return true;
    } else {
      return false;
    }
  }
}

var q = new Queue();
q.enqueue("Meredith");
q.enqueue("Cynthia");
q.enqueue("Jennifer");
console.log(q.toString());
q.dequeue();
console.log(q.toString());
console.log("Front of queue: " + q.front());
console.log("Back of queue: " + q.back());

```
### 使用ES6写一个字典类(关联数组)

```
class Dictionary {
  constructor() {
    this.datastore = new Array();
  }

  add(key, value) {
    this.datastore[key] = value;
  }

  find(key) {
    return this.datastore[key];
  }

  remove(key) {
    delete this.datastore[key];
  }

  showAll() {
    // keys =  Object.keys(this.datastore);
    // for (let [key, value] of this.datastore.entries()) {
    //    console.log(key, value);
    // }
    for (var key in this.datastore) {
      console.log(key + " -> " + this.datastore[key]);
    }
  }
  /**
   * 统计字典中的元素个数
   * 当键的类型为字符串时， length 属性就不管用了。
   */
  count() {
    var n = 0;
    for (var key in Object.keys(this.datastore)) {
      ++n;
    }
    return n;
  }

  /**
   * 清空
   */
  clear() {
    for (var key in this.datastore) {
      delete this.datastore[key];
    }
  }
}


var pbook = new Dictionary();
pbook.add("Mike", "123");
pbook.add("David", "345");
pbook.add("Cynthia", "456");
console.log(pbook.datastore);
console.log("David's extension: " + pbook.find("David"));
// pbook.remove("David");
pbook.showAll();
console.log(pbook.count());
console.log(pbook.clear());
console.log(pbook.datastore);
```


