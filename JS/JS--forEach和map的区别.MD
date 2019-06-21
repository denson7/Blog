## forEach和map的区别
对数组进行遍历，经常用到的两个方法就是forEach和map方法。

###相同点
```
1.两者都是循环遍历数组中的每一项 forEach和map方法里每次执行的匿名函数都支持3个参数，参数分别是item（当前每一项），index（索引值），arr（原数组）。
2.两者内部的匿名函数中的this都是指向window。
3.两者都只能遍历数组，都不会改变原数组。
```

### 不同点

#### forEach
forEach方法用来调用数组的每个元素，将元素传给回调函数 2.forEach对于空数组是不会调用回调函数的。
```
const arr = [0, 2, 4, 6, 8];
let sum = 0;
let str = arr.forEach(function(item, index, arr){
  sum += item;
  console.log("sum的值为：",sum); // 0 2 6 12 20
  console.log(this); // window
},this)
console.log(sum);// 20
console.log(str); // undefined
```

#### map
map方法返回一个新数组，数组中的元素为原始数组调用函数处理后的值。注意，如果是空数组，map不会检测也会返回一个空数组。
```
const arr = [0, 2, 4, 6, 8];
const res = arr.map((item, index, arr) => {
  console.log(this); // window
  console.log("原数组arr:", arr); // 注意这里执行5次 [0, 2, 4, 6, 8]
  return item / 2;
}, this);
console.log(res); // [0,1,2,3,4]
```
