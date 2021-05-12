## 示例说明
```javascript
var xw={
  name: "小王",
  gender: "男",
  age: 24,
  say: function(){
    alert(this.name+" , "+this.gender+" ,今年"+this.age);
  }
}
var xh={
  name: "小红",
  gender: "女",
  age: 18
}
xw.say();// 小王，男，今年24
```
显示的肯定是：小王 ， 男 ， 今年24。那么如何用xw的say方法来显示xh的数据呢？
```javascript
// 对于call可以这样
xw.say.call(xh); // 小红，女，今年18
// 对于apply可以这样
xw.say.apply(xh);
// 对于bind来说需要这样
xw.say.bind(xh)();
// 说明：call和apply都是对函数的直接调用，而bind方法返回的仍然是一个函数，因此后面还需要()来进行调用才可以。
```
如果有多个参数怎么办呢？
```javascript
var xw={
    name: "小王",
    gender: "男",
    age: 24,
    say: function(school,grade){
        alert(this.name+" , "+this.gender+" ,今年"+this.age+" ,在"+school+"上"+grade);
    }
}
var xh={
    name: "小红",
    gender: "女",
    age: 18
}
```
```javascript
// 对于call 传参，第一个参数绑定上下文，第二个参数是传递给调用函数say()的
xw.say.call(xh,"实验小学","六年级");
// 对于apply 传参
xw.say.apply(xh,["实验小学","六年级"]);
// 对于bind 传参
xw.say.bind(xh,"实验小学","六年级")();
// OR (推荐) 由于bind返回的仍然是一个函数，可以在调用的时候再进行传参
xw.say.bind(xh)("实验小学","六年级");
```
## 总结
### 共同点
1. `call`、`apply`、`bind`，三者都可以改变函数执行时的上下文，简单的说就是都可以`改变函数运行时的this指向`。
2. `call`、`apply`、`bind`， 三者第一个参数都是this要指向的对象。
3. `call`、`apply`、`bind`，三者都可以利用后续参数传参。
### 不同点
1. ` 传递参数的区别`，`call`和`aplly`的第一个参数都是要改变上下文的对象，而call从第二个参数开始以`参数列表`的形式展现，apply则是把除了改变上下文对象的参数放在一个`数组`里面作为它的第二个参数。
2. `返回不同`，`call`和`apply`改变了函数的this上下文后便执行该函数，而·bind·则是返回的是一个`函数`，因为bind()方法会创建一个新函数，称为绑定函数。

## 应用
### 获取数组中的最大值和最小值
```javascript
const  numbers = [5, 458 , 120 , -215 ];
const maxInNumbersByApply = Math.max.apply(Math, numbers);   //458
const maxInNumbersByCall = Math.max.call(Math,5, 458 , 120 , -215); //458
const minInNumbers = Math.min.apply(Math, numbers); // -215
```
### 验证是否是数组
```javascript
function isArray(obj){
  return Object.prototype.toString.call(obj) === '[object Array]' ;
}
```