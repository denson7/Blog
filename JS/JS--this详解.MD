### 判断this指向的对象
1. `this`的值通常是由所在函数的`执行环境`决定，也就是说要看函数是`如何被调用的`；
2. 同一个函数每一次调用，this都可能指向不同的对象；
### 全局对象(Global Object)
```
//在浏览器控制台直接
console.log(this);
//window{},window对象！ 因为在全局作用域下，this指向全局对象。在浏览器中全局对象就是window对象。
```
例如：
```
//在控制台输入：
var name = "denson"
//通过在控制台输入name来访问其值：
denson
//其实，所有在全局定义的变量都绑定到window对象。我们来做如下测试：
window.name
//输出 denson
window.name === "denson"
//输出true
```
将this放入到函数内部，测试this指向的对象
```
function test(){
  return this;
}
test();
//放在函数内部，你会发现this依然指向全局的window对象。因为this关键字没有处于一个声明的对象内部，默认指向全局window对象。
```


### 声明的对象(Declared Object)
当this关键字在一个声明对象的内部使用，其值会被`绑定`到`调用该this的函数`的`最近的父对象`。
例如:
```
//继续在浏览器控制台测试
var person = {
  first: 'Liu',
  last: 'Denson',  
  full: function() {
    console.log(this);
    console.log(this.first + ' ' + this.last);
  }
};
person.full();
//输出 Object {first: "Liu", last: "Denson", full: function}
// 输出 'Liu Denson'
```
在被声明的对象person中的full函数里面使用了this, 那么调用this的full函数的最近的父对象就是person, 因此，this指向person。

复杂的例子：
```
var person = {
  first: 'John',
  last: 'Smith',
  full: function() {
    console.log(this.first + ' ' + this.last);
  },
  personTwo: {
    first: 'Allison',
    last: 'Jones',
    full: function() {
      console.log(this.first + ' ' + this.last);
    }
  }
};

//在这里我们有嵌套的对象，在这个时候，this分别指向谁呢？ 我们将其打印出来看一看：
person.full();
// 输出 'John Smith'
person.personTwo.full();
// 输出 'Allison Jones'
```
可以看出规则就是，**`this其值会被绑定到调用this的函数的最近的父对象上`**。

### New 关键字
当使用`new`关键字构建一个新的对象，this会`绑定`到这个新对象。我们来看一个例子：
```
function Car(make, model) {
  this.make = make;
  this.model = model;
};
根据第一条规则，你可能会推断this指向全局对象。但是如果我们使用new关键字来声明一个新的变量，Car函数中的this将会绑定一个新的空对象，然后初始化this.make和this.model的值。

var myCar = new Car('Ford', 'Escape');
console.log(myCar);
// 输出 Car {make: "Ford", model: "Escape"}
```
### call, bind, 和apply
我们可以在call()，bind()，apply()中设置this的绑定对象。这三个函数很类似，但是我们需要注意它们微小的区别。
例子
```
function add(c, d) {
  console.log(this.a + this.b + c + d);
}
add(3,4);
// 输出 NaN
add函数输出NaN, 因为this.a和this.b未定义。
```
现在我们引入对象，并且使用call()和apply()来调用：
```
function add(c, d) {
  console.log(this.a + this.b + c + d);
}
var ten = {a: 1, b: 2};
add.call(ten, 3, 4);
// 输出 10
add.apply(ten, [3,4]);
// 输出 10
```
当我们使用add.call()，第一个参数是this需要绑定的对象，剩下的是add函数本来的参数。
因此，this.a指向ten.a,this.b指向ten.b。add.apply()也类似，除了第二个参数是一个数组，用于存储add函数的参数。

bind()函数和call()类似，但是bind()函数不会立即被调用。bind()函数会返回一个函数，并且将this绑定好。接下来我们来用例子来帮助理解bind()函数的应用场景：
```
var small = {
  a: 1,
  go: function(b,c,d){
    console.log(this.a+b+c+d);
  }
}
var large = {
  a: 100
}
执行:

small.go(2, 3, 4);
// 输出 10
```
如果我们想使用large.a的值，而不是small.a呢？ 我们可以使用call/apply:
```
small.go.call(large, 2, 3, 4);
// 输出 109
```
但是，如果我们现在还不知道这三个参数应该传入什么值，应该怎么办呢？ 我们可以使用bind:
```
var bindTest = small.go.bind(large, 2);
```
如果我们将bindTest在控制台下打印出来，我们会看到：
```
console.log(bindTest);
// 输出 function (b,c,d){console.log(this.a+b+c+d);}
```
注意：该函数已经将this绑定到large对象，并且传入了第一个参数b。所以，我们接下来是需要传入余下的参数即可：
```
bindTest(3, 4);
// 输出 109
```
### 总结
1. this的值通常是`由当前函数的执行环境`所决定；
2. 在`全局作用域`，this指向`全局对象` (window对象)；
3. 当使用`new关键字`声明，this指向`新建对象`；
4. 我们可以使用call(), bind(), apply()来设置this；














