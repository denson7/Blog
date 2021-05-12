## 示例
```javascript
// 对象的赋值为浅拷贝
let a = {name: 'A', age: 18};
let b = a;
b.name = 'B';
console.log(a); // 输出 {name: "B", age: 18}
console.log(b); // 输出 {name: "B", age: 18}

# 实现深拷贝
// 方法一： 使用JSON.parse(JSON.stringify(obj)),缺点是：会丢失成员函数
let a = {
  name: 'A',
  age: 18,
  work: function () {
    console.log('work')
  }
};
let c = JSON.parse(JSON.stringify(a))
c.name = 'C'
console.log(a) // 输出 {name: "A", age: 18, work:fn()}
console.log(c) // 输出 {name: "C", age: 18 }

// 方法二：使用Object.assign(),缺点是只有第一级是深拷贝,子级对象仍是浅拷贝
let a = {addr: {city: 'chengdu'}};
let b = Object.assign({}, a);
console.log(a) // 输出 {addr: {city: 'chengdu'}}
console.log(b) // 输出 {addr: {city: 'chengdu'}}
// 修改二级属性
b.addr.city = 'beijing'
console.log(a) // 输出 {addr: {city: 'beijing'}}
console.log(b) // 输出 {addr: {city: 'beijing'}}

// 方法三：手写一个深拷贝函数
function deepCopy(obj){
  // 判断是数组还是对象
  const result = obj instanceof Array ? [] : {};
  for(let prop in obj){
    if (typeof obj[prop]==='object' && obj[prop] !== null) {
      result[prop] = deepCopy(obj[prop]); //递归
    } else {
      result[prop] = obj[prop];
    }
  }
  return result;
}

let a = {
  name: 'A',
  age: 18,
  addr: {city: 'chengdu'},
  work: function () {
    console.log('work')
  }
};
let b = deepCopy(a)
console.log(a) // 输出 {name: "A", age: 18, addr: {city: 'chengdu'}, work:fn()}
console.log(b) // 输出 {name: "A", age: 18, addr: {city: 'chengdu'}, work:fn()}
b.addr.city = "beijing"
console.log(a) // 输出 {name: "A", age: 18, addr: {city: 'chengdu'}, work:fn()}
console.log(b) // 输出 {name: "A", age: 18, addr: {city: 'beijing'}, work:fn()}
```

