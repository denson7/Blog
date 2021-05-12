# 数组常用方法

## 常用方法
### 获取数组中的最大/小值
```javascript
// 使用apply
const arr = [2, 23, 61, 15, 20, '11'];
const max = Math.max.apply(null, arr); // 61
const min = Math.min.apply(null, arr); // 2

// 使用reduce
const max = arr.reduce((max, n) => n > max ? n : max);
const min = arr.reduce((min, n) => n < min ? n : min);

// 使用ES6扩展运算符
const max = Math.max(...arr); // 61
const min = Math.min(...arr); // 2

// 使用排序法
arr.sort((a, b) => a -b); // a-b(从小到大), b-a(从大到小)
console.log('最大值：', arr[arr.length - 1]); // 61
console.log('最小值：', arr[0]); // 2
```
### 遍历获取对象的key和value
```javascript
const obj = {
  objective: 'binary:logistic',
  numRound: 20,
  seed: 0,
  maxDepth: 6
}

#方法一
_.map(obj, (value, key) => {
  console.log(key, value)
})


#方法二
for(let key in obj) {
  const value = obj[key]
  console.log(key, value)
}

#方法三
// let {keys, values, entries} = Object
for (const [key, value] of Object.entries(obj)) {
  console.log(key, value)
}

// 结果
// objective binary:logistic
// numRound 20
// seed 0
// maxDepth 6

for ( const key of Object.keys(obj)){
  console.log(key)
}


for ( const val of Object.values(obj)) {
  console.log(val)
}
```
### 数组去重
```javascript
# 使用lodash的uniq()方法
var arr = [1, 2, 1, 2, '1', '11'];
_.uniq(arr);
// => [1, 2, "1", "11"]

# 使用ES6
var arr = [1, 2, 1, 2, '1', '11'];
Array.from(new Set(arr));//[1, 2, "1", "11"]
# 自定义函数
function unique(array){
  var newArr = [];//临时数组
  for(let i = 0;i < array.length; i++){
    if(newArr.indexOf(array[i]) === -1){
      newArr.push(array[i])
    };
  }
  return newArr;
}

console.log(unique(arr));//[1, 2, "1", "11"]
```
### 数组求和
```javascript
// reduce()方法用于对数组中的所有元素调用指定的回调函数，该回调函数的返回值为累积结果，并且将上一次计算的返回值作为参数传递给下一次调用的回调函数。
// array.reduce(callback[, initialValue]); //callback是调用方法,initialValue是callback初次调用时的第一个参数值
# 数组求和
const res1 = [1,2,3,4,5].reduce((prev,next)=>{
  return prev+next;
});
console.log(res1); // 15
# 对象求和
const obj = [{'p':1,'num':1},{'p':2,'num':2},{'p':3,'num':3},{'p':4,'num':4}];
const res2 = obj.reduce((prev,next)=>{
  return prev+next.p*next.num;
},0);
console.log(res2);// 30 (1+4+9+16)
```
### 统计数组中相同项的个数
```javascript
var cars = ['BMW','Benz', 'Benz', 'Tesla', 'BMW', 'Toyota'];
var carsObj = cars.reduce(function (obj, name) {
  obj[name] = obj[name] ? ++obj[name] : 1;
  return obj;
}, {});
console.log(carsObj); // => { BMW: 2, Benz: 2, Tesla: 1, Toyota: 1 }
```
### 过滤数组
```javascript
const users = [
  { name: 'tom', age: 10, isStudent: true },
  { name: 'larry', age: 20, isStudent: true },
  { name: 'john', age: 36, isStudent: false },
  { name: 'barney', age: 25, isStudent: false },
  { name: 'fred', age: 4, isStudent: false }
];
const res = _.filter(users, (item) => item.isStudent === true);
console.log(res);
# 工资的数组[1500, 1200, 2000, 2100, 1800],把工资超过2000的删除
const arr = [1500, 1200, 2000, 2100, 1800];
const newArr = arr.filter(function (ele, i, array) {
  //2000以上返回false;
  console.log(ele,i,array);// ele表示当前元素,i表示索引,array表示当前数组
  return ele<2000 ? true : false;
});
console.log(newArr);//[1500, 1200, 1800]
```
### 删除数组中的空值
```javascript
// 使用filter过滤
const arr = [undefined,undefined,1,'','false',false,true,null,'null']
const res = _.filter(arr, o => o) // [1, "false", true, "null"]
// 使用compact去除假值(将所有的false, null, 0, "", undefined, 和 NaN 过滤)
const res2 = _.compact(arr) //  [1, "false", true, "null"]
```
### 删除数组中的某个值
```javascript
const ages = [32, 33, 16, 40]
const res = ages.filter((item)=>item != 40)
console.log(res) // [32, 33, 16]
```
### 求两个数组的交集
```javascript
var arr1 = [1, 2, 3, 4, 5];
var arr2 = [4, 5, 6, 7, 8, 9]
var result = arr1.filter(function(n){ return arr2.indexOf(n) > -1 });
console.log(result); // [4, 5]
```
### 对数组对象进行排序
```javascript
#方法一自定义函数
function orderByProperty(prop) {
    var args = Array.prototype.slice.call(arguments, 1);
    return function (a, b) {
        var equality = a[prop] - b[prop];
        if (equality === 0 && arguments.length > 1) {
            return orderByProperty.apply(null, args)(a, b);
        }
        return equality;
    };
}

var data = [
    { a: 1, b: 2 },
    { a: 2, b: 3 },
    { a: 2, b: 1 },
    { a: 1, b: 3 },
    { a: 2, b: 2 },
    { a: 1, b: 1 },
];

var sortedData = data.sort(orderByProperty('a', 'b'));

// sortedData = [
//   { a: 1, b: 1 },
//   { a: 1, b: 2 },
//   { a: 1, b: 3 },
//   { a: 2, b: 1 },
//   { a: 2, b: 2 },
//   { a: 2, b: 3 },
// ]
#方法二
var obj = [
    {"one": 1,"two": 9},
    {"one": 3,"two": 5},
    {"one": 1,"two": 2}
];
sort = obj.sort(function(a, b) {
    return a["one"] - b["one"] || a["two"] - b["two"];
});
console.log(sort)
#方法三 引用lodash（推荐）
var users = [
    { 'user': 'fred',   'age': 48 },
    { 'user': 'barney', 'age': 36 },
    { 'user': 'fred',   'age': 40 },
    { 'user': 'barney', 'age': 34 }
];
_.sortBy(users, [function(o) { return o.user; }]);
// => objects for [['barney', 36], ['barney', 34], ['fred', 48], ['fred', 40]]
_.sortBy(users, ['user', 'age']);
// => objects for [['barney', 34], ['barney', 36], ['fred', 40], ['fred', 48]]
```

### 从数组对象中根据一个对象中的值，获取该对象
```javascript
#使用lodash
var users = [
  { 'user': 'barney', 'age': 36, 'active': true },
  { 'user': 'fred', 'age': 40, 'active': false },
  { 'user': 'pebbles', 'age': 1, 'active': true }
];

_.find(users, function(o) { return o.age < 40; });
// => object for 'barney'

// The `_.matches` iteratee shorthand.
_.find(users, { 'age': 1, 'active': true });
// => object for 'pebbles'

// The `_.matchesProperty` iteratee shorthand.
_.find(users, ['active', false]);
// => object for 'fred'

// The `_.property` iteratee shorthand.
_.find(users, 'active');
// => object for 'barney'

```
### 判断数组中是否存在某个值
```javascript
#使用lodash (推荐)
const a1 = ['a','b','c']
const _index = _.findIndex(a1,(item)=>(item==='b'))
console.log(_index) //1
#使用lodash的includes函数
_.includes([1, 2, 3], 3); // true

#自定义函数
function isInArray(arr,value){
  for(var i = 0; i < arr.length; i++){
    if(value === arr[i]){
      return true;
    }
  }
  return false;
}
#ES6使用includes函数
const arr = ["Sam", "Great", "Sample", "High"]
arr.includes("Sam")  //true
#使用indexOf函数,判断索引大于-1 （推荐）
const arr = ["Sam", "Great", "Sample", "High"]
arr.indexOf("Sam") > -1 
```
### 获取一个数组对象中某一列的所有key值
```javascript
#使用lodash
const list = [
  { title: '模型名称', id: '1', key:'model_name' },
  { title: '模型版本', id: '2', key:'model_version' },
  { title: '任务类型', id: '3', key:'task_type' },
];
const keys = _.map(list,(item) => (item.key))
console.log(keys) // ["model_name", "model_version", "task_type"]
```
### 判断一个数组是否包含另一个数组
```javascript
/**
 * 判断一个数组是否包含另一个数组
 * @param a
 * @param b
 * @return {boolean}
 */
export const isContained = (a, b) => {
  if (!(a instanceof Array) || !(b instanceof Array)) return false
  if (a.length < b.length) return false
  const aStr = a.toString()
  for (let i = 0, len = b.length; i < len; i++) {
    if (aStr.indexOf(b[i]) === -1) return false
  }
  return true
}
```
### 二维(多维)数组转为一维数组
```js
#二维数组转化为一维数组
// 方法一
const res = [[1,2],[3,4]].reduce(function (a, b) { return a.concat(b)} );
console.log(res); // [1,2,3,4]

// 方法二
const arr = [11, [22, 33], [44, 55], 66];
const flatArr = [].concat(...arr); //=> [11, 22, 33, 44, 55, 66]
console.log(flatArr); //[11, 22, 33, 44, 55, 66, 77, 88, 99]

#多维数组转为一维数组
// 方法一：使用...扩展运算符
function flattenArray(arr) {
  const flattened = [].concat(...arr);
  return flattened.some(item => Array.isArray(item)) ?
    flattenArray(flattened) : flattened;
}

const arr1 = [11, [22, 33], [44, [55, 66, [77, [88]], 99]]];
const flatArr1 = flattenArray(arr1);
console.log(flatArr1); //[11, 22, 33, 44, 55, 66, 77, 88, 99]

// 方法二：使用递归
function flatten(arr){
  var res = [];
  for(var i=0;i<arr.length;i++){
    if(Array.isArray(arr[i])){
      res = res.concat(flatten(arr[i]));
    }else{
      res.push(arr[i]);
    }
  }
  return res;
}

const arr2 = [11, [22, 33], [44, [55, 66, [77, [88]], 99]]];
const flatArr2 = flatten(arr2);
console.log(flatArr2); //[11, 22, 33, 44, 55, 66, 77, 88, 99]

// 方法三：使用reduce
function flatten(arr){
  return arr.reduce(function(prev,item){
    return prev.concat(Array.isArray(item)?flatten(item):item);
  },[]);
}

const arr3 = [11, [22, 33], [44, [55, 66, [77, [88]], 99]]];
const flatArr3 = flatten(arr3);
console.log(flatArr3); //[11, 22, 33, 44, 55, 66, 77, 88, 99]
```
## 不改变自身的方法
### slice() 
拷贝数组中的一部分，返回拷贝后的新数组
```js
let arr = [1,2,3,4,5];
let arr1 = arr.slice(1,3); // arr是[1,2,3,4,5]， arr1是[2,3]
let arr2 = arr.slice(-2,-1);  // arr是[1,2,3,4,5], arr2是[4]
```
### join() 
将数组转换为字符串，返回字符串
```js
let num = [1,2,3];
let str1 = num.join(); // 1,2,3 默认是逗号连接
let str2 = num.join(', ') // 1, 2, 3
let str3 = num.join('') // 123
//如果元素是undefined 或者null， 则会转化成空字符串。
let num = [1,null,3];
let str1 = num.join(); // 1,,3
//如果数组中的元素是数组，会将里面的数组也调用join()
let num = [[1,2],3];
let str1 = num.join('-'); // 1,2-3

// 扁平化简单的二维数组
const arr = [11, [22, 33], [44, 55], 66];
const flatArr = arr.join().split(','); // ["11", "22", "33", "44", "55", "66"]
```
### toString() 
转换数组为字符串，并输出以逗号分隔的字符串列表
```js
[1,2,3].toString(); // 1,2,3
[1,[2,'c']].toString(); //1,2,c
```
### concat() 
合并两个或多个数组，返回合并后的新数组
```js
[1,2,3].concat([4,5,6],[7,8,9]) // [1, 2, 3, 4, 5, 6, 7, 8, 9]
['a','b','c'].concat(1,[2,3],[[4,5]]) // ["a", "b", "c", 1, 2, 3, [4,5]]

// concat 和扩展运算符可以快速扁平化数组
const arr = [11, [22, 33], [44, 55], 66];
const flatArr = [].concat(...arr); // [11, 22, 33, 44, 55, 66]
```
## 会改变自身的方法
### push() 
往数组的尾部添加一个或多个元素，并返回数组长度
```js
let arr = [1,2,3];
let length = arr.push('aa','bb');    // 返回数组长度
console.log(arr,length)
// [1, 2, 3, "aa", "bb"] 5
```
### prop() 
删除数组的最后一个元素，并返回删除的值
```js
let arr= [1,2,3];
arr.pop()    // 返回删除的值3
```
### unshift() 
往数组的头部添加一个或多个元素，并返回数组的长度
```js
let arr = [3,4,5];
let length = arr.unshift(1,2);  // 返回长度是5
console.log(arr, length)
//[1, 2, 3, 4, 5] 5
```
### shift() 
删除数组的第一个元素,并返回删除的值
```js
let arr = [1,2,3];
let item = arr.shift(); // 返回删除的值1
console.log(arr, item)
// [2, 3] 1
```
### splice() 
删除数组中的元素，返回原数组删除后的值（亦可在指定位置插入元素）
```js
// start不超过数组长度(以下操作是连续的)
let arr = [1,2,3,4,5];
arr.splice(2)  // arr是[1,2]，返回值是[3,4,5]
arr.splice(1,1) // arr是[1]，返回值是[2]
arr.splice(0,3) // arr是[]，返回值是[1],因为此时数组从第0位开始不够3位，所以是删除从0开始到最后的所有元素。

// start大于数组长度(以下操作是连续的)
let arr = [1,2,3,4,5];
arr.splice(5)  // arr是[1,2,3,4,5]，返回值是[]
arr.splice(5,3,6) // arr是[1,2,3,4,5,6]，返回值是[]
arr.splice(5,3,7) // arr是[1,2,3,4,5,7] 返回值是[6]

// start是负数(以下操作是连续的)
let arr = [1,2,3,4,5];
arr.splice(-3,2); // arr是[1,2,5], 返回值是[3,4]
arr.splice(-4); // arr是[],返回值是[1,2,5]

// 插入数组时，是插入数组本身，而不是数组元素
let arr = [1,4,5];
arr.splice(1,0,[2,3])  // arr是[1,[2,3],4,5]，返回值是[]
arr.splice(index, 0, val) // index插入位置的索引，val表插入的元素
```
### sort()
数组排序，返回排序后的数组
`备注：`sort()函数比较的是ASCII码的大小，而且数组的sort()方法默认是把所有元素先转换为String再排序，所以，直接使用sort()排序，会出现以下问题：
```js
// baiDu排在了最后:
['Google', 'baiDu', 'Facebook'].sort(); // ['Facebook', 'Google", 'baiDu']

// 对于数字而言，就是无法理解的结果了:
[10, 20, 1, 2].sort(); // [1, 10, 2, 20]
//转换成字符串比较，'10'排在了'2'的前面，因为字符'1'比字符'2'的ASCII码小
#正确的方法，使用比较函数
[10, 20, 1, 2].sort((a, b) => {
    return a - b;
});
// 结果
[1, 2, 10, 20]
```
### reverse() 
将数组中的元素颠倒顺序，返回逆序的数组
```js
let arr = [1,2,3];
arr.reverse()  // arr是[3,2,1]，返回值是[3,2,1]
```
### fill() 
用一个固定值填充一个数组中从起始索引到终止索引内的全部元素
```js
[1, 2, 3].fill(4);              // [4, 4, 4]
[1, 2, 3].fill(4, 1);            // [1, 4, 4]
[1, 2, 3].fill(4, 1, 2);        // [1, 4, 3]
[1, 2, 3].fill(4, 1, 1);        // [1, 2, 3]
[1, 2, 3].fill(4, 3, 3);        // [1, 2, 3]
[1, 2, 3].fill(4, -3, -2);      // [4, 2, 3]
[1, 2, 3].fill(4, NaN, NaN);    // [1, 2, 3]
[1, 2, 3].fill(4, 3, 5);        // [1, 2, 3]
Array(3).fill(4);                // [4, 4, 4]
```


