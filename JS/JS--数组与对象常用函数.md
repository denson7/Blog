## 对象常用方法
### 对象数据操作
```javascript
const obj = {
  name: 'demo-name',
  id: '1234565',
  nickName: 'nick',
  num: 10202,
  price: 200,
  tag: 0,
};

// 需求1：过滤对象中我不要的数据
const filterArr = ['nickName', 'tag']; //不需要的字段
const result1 = Object.keys(obj)
  .filter((item) => !filterArr.includes(item))
  .reduce((o, key) => ((o[key] = obj[key]), o), {});
/**
 { "name": "demo-name", "id": "1234565", "num": 10202, "price": 200 }
 */

// 需求2：只提取我要的数据
const includeArr = ['name', 'price', 'num'];
// JSON.stringify(value[, replacer[, space]]), 第二个参数用于转换结果的函数或数组。
// 若 replacer 为函数，则 JSON.stringify 将调用该函数，并传入每个成员的键和值。使用返回值而不是原始值。如果此函数返回 undefined，则排除成员。根对象的键是一个空字符串：""。
// 若 replacer 是一个数组，则仅转换该数组中具有键值的成员。成员的转换顺序与键在数组中的顺序一样。
// space 可选，文本添加缩进、空格和换行符
const result2 = JSON.parse(JSON.stringify(obj, includeArr));
/**
 * 输出
 { "name": "demo-name", "price": 200, "num": 10202 }
 */

// 需求3：覆盖数据
const result3 = Object.assign(obj, { tag: 1 });
/**
 * 输出
 { "name": "demo-name", "id": "1234565", "nickName": "nick", "num": 10202, "price": 200, "tag": 1 }
 */

// 需求4：单字段映射
const result4 = JSON.parse(JSON.stringify(obj).replace(/name/g, 'Name'));
/**
 * 输出
 { "Name": "demo-Name", "id": "1234565", "nickName": "nick", "num": 10202, "price": 200, "tag": 1 }
 */

// 需求5：数据映射
// 例如：当字段tag为0，1时，需要转换成为相对应的值，如0为禁用，1为可用，进行映射转换
const result5 = JSON.parse(
  JSON.stringify(obj, (key, value) => {
    if (key == 'tag') {
      return ['禁用', '可用'][value];
    }
    return value;
  })
);
/**
 * 输出
 { "name": "demo-name", "id": "1234565", "nickName": "nick", "num": 10202, "price": 200, "tag": "可用" }
 */
// 需求6：数据合并
const query = { weight: '170' };
const result6 = { ...obj, query };
/**
 * 输出
 { "name": "demo-name", "id": "1234565", "nickName": "nick", "num": 10202, "price": 200, "tag": 1,
  "query": {
    "weight": "170"
  }
}
 */
```
### 移除对象中key-value的首尾空格
```javascript
// 移除对象中key-value的首尾空格
// 方法1
const trimObj1 = (obj) => {
  if (!Array.isArray(obj) && typeof obj !== 'object') {
    return obj;
  }
  return Object.keys(obj).reduce(
    (acc, key) => {
      acc[key.trim()] = typeof obj[key] === 'string' ? obj[key].trim() : trimObj1(obj[key]);
      return acc;
    },
    Array.isArray(obj) ? [] : {}
  );
};

// 方法2
const trimObj2 = (obj) => {
  return JSON.parse(JSON.stringify(obj).replace(/"\s+|\s+"/g, '"'));
};

// 移除对象中value的首尾空格
// 方法2
const trimObj3 = (obj) => {
  return JSON.parse(JSON.stringify(obj), (key, value) => {
    if (typeof value === 'string') {
      return value.trim();
    }
    return value;
  });
};
```
### 判断对象是否为空
```javascript
// 判断对象是否为空
export const isEmpty = (obj) => {
    // 方法1
    return Object.keys(obj).length;
    // 方法2
    // return JSON.stringify(obj) == "{}";
    // 方法3
    // for(let key in obj) {
    //     if(obj.hasOwnProperty(key))
    //         return false;
    // }
    // return true;
    // 方法4 lodash
    // return _.isEmpty(obj)
};
```
### 将一个对象转化为url参数形式
```javascript
// 将对象序列化为url参数
export const convertObjToUrlParams = (item) => Object.entries(item).map(([key, val]) => `${key}=${encodeURIComponent(val)}`).join('&');
```
### 如何从对象中查询指定的属性
```javascript
const get = (from, ...selectors) =>
  [...selectors].map(s =>
    s
      .replace(/\[([^\[\]]*)\]/g, '.$1.')
      .split('.')
      .filter(t => t !== '')
      .reduce((prev, cur) => prev && prev[cur], from)
  );
// 示例
const obj = { selector: { to: { val: 'val to select' } }, target: [1, 2, { a: 'test' }] };

// Example
get(obj, 'selector.to.val', 'target[0]', 'target[2].a');
// ['val to select', 1, 'test']
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
### 选取对象中指定键值
```javascript
const pick= (obj = {}, keys = []) => {
  return Object.keys(obj).reduce((t, v) => (keys.includes(v) && (t[v] = obj[v]), t), {});
};

const target = {a: 1, b: 2, c: 3, d: 4};
const keyword = ["a", "d"];
pick(target, keyword); // { a: 1, d: 4 }
```
### 忽略对象中属性
```javascript
// 忽略对象中指定的属性
const omit = (obj, keys) =>
  Object.keys(obj)
    .filter((k) => !keys.includes(k))
    .reduce((res, k) => Object.assign(res, { [k]: obj[k] }), {});

// Example
omit({ a: "1", b: "2", c: "3" }, ["a", "b"]); // { c: '3' }
```
### 删除对象中的属性
```javascript
// 删除对象中的属性
export const removeObjAttr = (obj: Record<string, any>, keys: string[] = []) => {
  if (keys.length) {
    for (const key of keys) {
      delete obj[key];
    }
  } else {
    for (const k in obj) {
      delete obj[k];
    }
  }
  return obj;
};
```
### 数组key-value转换为对象
```javascript
const toObj = (arr) => Object.fromEntries(arr);
//Or
const toObj = (arr) => arr.reduce((a, c) => ((a[c[0]] = c[1]), a), {});

// Example
toObj([
  ["a", 1],
  ["b", 2],
  ["c", 3],
]);
// { a: 1, b: 2, c: 3 }
```
### 对象key-value转换为数组
```javascript
const ObjKeyValueToArray = object => Object.keys(object).map(el => [el, object[el]]);

ObjKeyValueToArray({ x: 1, y: 2, z: 3 });
// [ ['x', 1], ['y', 2], ['z', 3] ]
```
### 反转对象的key-value
```javascript
const invert = (obj) =>
  Object.keys(obj).reduce((res, k) => Object.assign(res, { [obj[k]]: k }), {});

// Example
invert({ a: "1", b: "2", c: "3" }); 
// { 1: 'a', 2: 'b', 3: 'c' }
```



## 数组常用方法
### 自定义遍历器
```javascript
const allAuthors = {
  fiction: ['Agatha Chars', 'b', 'C', 'DFs'],
  science: ['Neal', 'Arthur', 'Dss', 'Es'],
  fantasy: ['JK', 'JM'],
};

const author = new Proxy(allAuthors, {
  has(target, key) {
    const keys = Reflect.ownKeys(target).slice(0, -1);
    for (const item of keys) {
      if (target[item].includes(key)) {
        return true;
      }
    }
    return false;
  },
});
// 自定义遍历器
author[Symbol.iterator] = function* () {
  // 保存当前对象
  const all = this;
  // 获取所有的key
  const keys = Reflect.ownKeys(this).slice(0, -1);
  let values = [];
  while (1) {
    if (!values.length) {
      // 判断keys是否取完
      if (keys.length) {
        values = [].concat(all[keys[0]]);
        // 移除keys中的元素
        keys.shift();
        yield values.shift();
      } else {
        // 遍历完
        return false;
      }
    } else {
      yield values.shift();
    }
  }
};
console.log('JK', 'JK' in author);
for (const item of author) {
  console.log(item);
}
```
### 数组转对象示例
```javascript
const people = [
  {area: "GZ", name: "YZW", age: 27},
  {area: "SZ", name: "TYJ", age: 25}
];
const map = people.reduce((t, v) => {
  const {name, ...rest} = v;
  t[name] = rest;
  return t;
}, {}); // { YZW: {…}, TYJ: {…} }
```
### 数组对象转换为csv格式
```javascript
// 数组对象转换为csv格式类型
export const convertArrayToCSV = (objArray) => {
  let array = typeof objArray != 'object' ? JSON.parse(objArray) : objArray;
  let str = '';
  for (let i = 0; i < array.length; i++) {
    let line = '';
    for (let index in array[i]) {
      if (line !== '') {
        line += ','
      }
      line += array[i][index];
    }
    str += line + '\r\n';
  }
  return str;
};

convertArrayToCSV([{a: 1, b: 2, c: 3}]); // 1,2,3\r\n
```
### 数组随机排序
```javascript
function randomSort1(arr) {
  for (let i = 0, l = arr.length; i < l; i++) {
    let rc = parseInt(Math.random() * l)
    // 让当前循环的数组元素和随机出来的数组元素交换位置
    const empty = arr[i]
    arr[i] = arr[rc]
    arr[rc] = empty
  }
  return arr
}

function randomSort2(arr) {
  const mixedArr = []
  while (arr.length > 0) {
    let rc = parseInt(Math.random() * arr.length)
    mixedArr.push(arr[rc])
    arr.splice(rc, 1)
  }
  return mixedArr
}


function randomSort3(arr) {
  arr.sort(function (a, b) {
    return Math.random() - 0.5
  })
  return arr
}
```
```javascript
/**
 * 打乱数组
 * @param array
 * @returns {any[]}
 eg:
 const array = Array(5).fill().map((_, i) => i)
 const shuffled = shuffle(array)
 */
const shuffle = array => {
  if (!Array.isArray(array)) {
    throw new TypeError("'array' is not an array")
  }
  if (array.length < 2) {
    return array
  }
  const copy = array.slice()
  for (let i = copy.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1))
    const temp = copy[i]
    copy[i] = copy[j]
    copy[j] = temp
  }
  return copy
}
```
### 判断是否为数组
```javascript
const isArray = (arg: any) => {
  if (typeof Array.isArray === 'undefined') {
    return Object.prototype.toString.call(arg) === '[object Array]'
  }
  return Array.isArray(arg)
}
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
### 判断数组是否为空
```javascript
const isEmptyArr = (arr = []) => Array.isArray(arr) && !arr.length;
```
### 判断两个数组或对象是否相同
```javascript
/**
 * 比较两个数组或对象是否相同
 * @param value
 * @param other
 * @return {boolean}
 */
const isEqual = (value, other) => {
  // 获取value的类型
  const type = Object.prototype.toString.call(value);
  // 如果两个items的类型不同,则返回false
  if (type !== Object.prototype.toString.call(other)) return false;
  // 如果两个items不是对象或数组，则返回false
  if (['[object Array]', '[object Object]'].indexOf(type) < 0) return false;
  // 比较两个items的长度
  // 获取value的长度
  const valueLen = type === '[object Array]' ? value.length : Object.keys(value).length;
  // 获取other的长度
  const otherLen = type === '[object Array]' ? other.length : Object.keys(other).length;
  if (valueLen !== otherLen) return false;

  // 比较两个item
  const compare = function (item1, item2) {
    // Get the object type
    const itemType = Object.prototype.toString.call(item1);
    // If an object or array, compare recursively
    if (['[object Array]', '[object Object]'].indexOf(itemType) >= 0) {
      if (!isEqual(item1, item2)) return false;
    } else {
      // Otherwise, do a simple comparison
      // If the two items are not the same type, return false
      if (itemType !== Object.prototype.toString.call(item2)) return false;
      // Else if it's a function, convert to a string and compare
      if (itemType === '[object Function]') {
        if (item1.toString() !== item2.toString()) return false;
      } else { // Otherwise, just compare
        if (item1 !== item2) return false;
      }
    }
  };

  // 比较数组
  if (type === '[object Array]') {
    for (let i = 0; i < valueLen; i++) {
      if (compare(value[i], other[i]) === false) return false;
    }
  } else { // 比较对象
    for (let key in value) {
      if (value.hasOwnProperty(key)) {
        if (compare(value[key], other[key]) === false) return false;
      }
    }
  }
  // If nothing failed, return true
  return true;
};

const one = {
  name: 'obj',
  aa:["123","345",[1,2,3,4,5]],
  bb:{ cc: {name:'name', age:'12'} },
  dd: function (val) {console.log(val)}
};

const two = {
  name: 'obj',
  aa:["123","345",[1,2,3,4,5]],
  bb:{ cc: {name:'name', age:'12'} },
  dd: function (val) {console.log(val)}
};
// 方法一,使用 JSON.stringify()序列化后进行比较
// 缺点是：仅能比较一级
// JSON.stringify(one) === JSON.stringify(two) //

// 方法二,使用 lodash,
// 缺点是无法比较对象中有属性为函数的情况
const isTrue = _.isEqual(one,two)

// 方法三, 使用自定义函数
const isEq = isEqual(one, two)
```
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
### 判断数组中是否有重复元素
```javascript
    const isRepeat = (arr) => {
      let hash = {};
      for(var i in arr) {
        if(hash[arr[i]]) {
          return true;
        }
        hash[arr[i]] = true;
      }
      return false;
    }
```
### 数组去重
```javascript
// 方法1：使用lodash的uniq()方法
var arr = [1, 2, 1, 2, '1', '11'];
_.uniq(arr);
// => [1, 2, "1", "11"]

// 方法2：使用ES6
const arr = [1, 2, 1, 2, '1', '11'];
const list = Array.from(new Set(arr)); //[1, 2, "1", "11"] // 或 const list = [...new Set(arr)];
// Or
const unique = (arr) => [...new Set(arr)];

// 方法3：使用reduce
const unique = (arr = []) => {
  return arr.reduce((t, v) => t.includes(v) ? t : [...t, v], []);
};
// 方法4：自定义函数
function unique(array){
  var newArr = [];//临时数组
  for(let i = 0;i < array.length; i++){
    if(newArr.indexOf(array[i]) === -1){
      newArr.push(array[i])
    };
  }
  return newArr;
}

// 方法5
const unique = (arr) => arr.filter((el, i, array) => array.indexOf(el) === i);

console.log(unique(arr));
// [1, 2, "1", "11"]
```
### 数组对象去重
```javascript
var a = [{a: 1, b: 1}, {a: 2, b: 2}];
var b = [{a: 10, b: 10}, {a: 2, b: 2}];
// 合并
var arr = a.concat(b);
console.log(arr); // [{"a": 1, "b": 1}, {"a": 2, "b": 2}, {"a": 10, "b": 10}, {"a": 2, "b": 2}]
// 去重
var res = _.uniqWith(arr, _.isEqual);
console.log(res);// [{"a": 1, "b": 1}, {"a": 2, "b": 2}, {"a": 10, "b": 10}]
```
```javascript
// 根据对象数组中某一个属性进行过滤
// const demo = [{key:'1', value:'11'}, {key:'2', value:'22'}, {key:'1', value:'11'}]
export const uniqueObjByKey = (arr) => {
  const obj = {};
  arr = arr.reduce((item, next) => {
    obj[next.key] ? '' : (obj[next.key] = true && item.push(next));
    return item;
  }, []);
  return arr;
};
// 根据属性去重
function unique(arr) {
    const res = new Map()
    return arr.filter((item) => !res.has(item.xxx) && res.set(item.xxx, 1))
}
```
```javascript
// 比较对象中的每一个属性
export const uniqueObjArray = (array) => {
  if (!(array instanceof Array)) {
    return -1;
  }
  const newArr = [array[0]];
  for (let i = 1; i < array.length; i++) {
    let isIn = false;
    for (let j = 0; j < newArr.length; j++) {
      // 判断连个对象是否相等,使用自定义函数 isEqual
      if (isEqual(array[i], newArr[j])) {
        isIn = true;
        break;
      }
    }
    if (!isIn) {
      newArr.push(array[i]);
    }
  }
  return newArr;
};
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
### 数组分割
```javascript
const chunk = (arr = [], size = 1) => {
  return arr.length ? arr.reduce((t, v) => (t[t.length - 1].length === size ? t.push([v]) : t[t.length - 1].push(v), t), [[]]) : [];
};

const arr = [1, 2, 3, 4, 5];
chunk(arr, 2); // [[1, 2], [3, 4], [5]]
```
```javascript
/**
 * split the array by step size
 * @param data
 * @param step
 * eg:
 * originArr = [{a:1}, {b:1}, {c:1}, {d:1}, {e:1}]
 * newArr = [[{a:1},{b:1}], [{c:1},{d:1}], [{e:1}]]
 */
export const splitArr = (data: any, step = 1) => {
  const newArr = [];
  for (let i = 0; i < data.length; i += step) {
    newArr.push(data.slice(i, i + step));
  }
  return newArr;
};

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
### 数组成员位置记录
```javascript
const Position = (arr = [], val) => {
  return arr.reduce((t, v, i) => (v === val && t.push(i), t), []);
};
const arr = [2, 1, 5, 4, 2, 1, 6, 6, 7];
Position(arr, 2); // [0, 4]
```
### 数组对象根据某一个属性分组
```javascript
const Group = (arr = [], key) => {
  return key ? arr.reduce((t, v) => (!t[v[key]] && (t[v[key]] = []), t[v[key]].push(v), t), {}) : {};
};
const arr = [
  { area: "GZ", name: "YZW", age: 27 },
  { area: "GZ", name: "TYJ", age: 25 },
  { area: "SZ", name: "AAA", age: 23 },
  { area: "FS", name: "BBB", age: 21 },
  { area: "SZ", name: "CCC", age: 19 }
]; // 以地区area作为分组依据
Group(arr, "area"); // { GZ: Array(2), SZ: Array(2), FS: Array(1) }
```
### 数组分组
```javascript
/**
 *
 * @param {*} array
 * @param {*} groupFn
 eg:
 const input = [1, 2, 3, 4]
 const groupFn = async item => (item % 2 === 0 ? 'even' : 'odd')
 const output = await groupBy(input, groupFn)
 // output
 new Map([
   ['even', [2, 4]],
   ['odd', [1, 3]],
 ])
 */
const groupBy = async (array, groupFn) => {
  const result = new Map()
  for (const item of array) {
    const group = await Promise.resolve(groupFn(item))
    result.set(group, result.has(group) ? [...result.get(group), item] : [item])
  }
  return result
}
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
const arr = [undefined,undefined,1,'','false',false,true,null,'null'];
const res = _.filter(arr, o => o); // [1, "false", true, "null"]
// 使用compact去除假值(将所有的false, null, 0, "", undefined, 和 NaN 过滤)
const res2 = _.compact(arr); //  [1, "false", true, "null"]

// 自定义函数
const removeFalsy = arr => arr.filter(Boolean);
// Example
removeFalsy([0, 'a string', '', NaN, true, 5, undefined, 'another string', false]); // ['a string', true, 5, 'another string']
```
### 删除数组中的某个值
```javascript
const ages = [32, 33, 16, 40]
const res = ages.filter((item)=>item != 40)
console.log(res) // [32, 33, 16]
```
### 数组交集
```javascript
// 方法1
// space: O(n)
// time: O(n)
const getIntersection1 = (...arr) =>
  [
    ...arr
      .flat()
      .reduce((map, v) => map.set(v, (map.get(v) || 0) + 1), new Map()),
  ].reduce(
    (acc, [v, count]) => void (count === arr.length && acc.push(v)) || acc,
    []
  );

// 方法2
// Only support two arrays
const getIntersection2 = (a, b) => [...new Set(a)].filter((v) => b.includes(v));

// Examples
getIntersection2([1, 2, 3], [2, 3, 4, 5]); // [2, 3]
getIntersection1([1, 2, 3], [2, 3, 4, 5], [1, 3, 5]); // [3]

// 方法3
const arr1 = [1, 2, 3, 4, 5];
const arr2 = [4, 5, 6, 7, 8, 9];
const result = arr1.filter((n) => {
  return arr2.indexOf(n) > -1;
});
console.log(result); // [4, 5]

// 方法4
// array intersection 交集
const inter2Arr = (a, b) => {
  return a.filter((v) => b.includes(v));
};
```
### 数组并集
```javascript
const union = (...arr) => [...new Set(arr.flat())];
// Example
union([1, 2], [2, 3], [3]);
// [1, 2, 3]

// array union 并集
export const union2Arr = (a: any[], b: any[]) => {
  return a.concat(b.filter((v) => !a.includes(v)));
};
```
### 数组差集
```javascript
// array difference 差集
export const diff2Arr = (a, b) => {
  return a.concat(b).filter((v) => a.includes(v) && !b.includes(v));
};
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
// 示例
const group = (arr = [], key) => {
  return key ? arr.reduce((t, v) => (!t[v[key]] && (t[v[key]] = []), t[v[key]].push(v), t), {}) : {};
};

const arr = [
  { scan_type: "IN", created_at: "2018-12-10 11:11:54" },
  { scan_type: "OUT", created_at: "2018-12-10 09:21:01" },
  { scan_type: "IN", created_at: "2018-12-10 10:30:50" },
  { scan_type: "OUT", created_at: "2018-12-10 11:34:53" },
  { scan_type: "IN", created_at: "2018-12-10 12:22:22" },
  { scan_type: "OUT", created_at: "2018-12-10 13:23:33" },
];

const { IN: arr1, OUT: arr2 } = group(arr, "scan_type");
const compare = (property, desc = false) => {
  return function (a, b) {
    const x = a[property];
    const y = b[property];
    // ASC 升序
    let result = x < y ? -1 : x > y ? 1 : 0;
    if (desc) {
      // DESC 降序
      result = x > y ? -1 : x < y ? 1 : 0;
    }
    return result;
  };
}
arr1.sort(compare("created_at"));
arr1.sort(compare("created_at", true));

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
### 二维(多维)数组转为一维数组
```javascript
# 二维数组转化为一维数组
// 方法一
const res = [[1, 2], [3, 4]].reduce(function (a, b) {
    return a.concat(b)
});
console.log(res); // [1,2,3,4]

// 方法二
const arr = [11, [22, 33], [44, 55], 66];
const flatArr = [].concat(...arr); //=> [11, 22, 33, 44, 55, 66]
console.log(flatArr); //[11, 22, 33, 44, 55, 66, 77, 88, 99]

# 多维数组转为一维数组
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
function flatten(arr) {
    let res = [];
    for (let i = 0; i < arr.length; i++) {
        if (Array.isArray(arr[i])) {
            res = res.concat(flatten(arr[i]));
        } else {
            res.push(arr[i]);
        }
    }
    return res;
}

const arr2 = [11, [22, 33], [44, [55, 66, [77, [88]], 99]]];
const flatArr2 = flatten(arr2);
console.log(flatArr2); //[11, 22, 33, 44, 55, 66, 77, 88, 99]

// 方法三：使用reduce
function flatten(arr) {
    return arr.reduce(function (prev, item) {
        return prev.concat(Array.isArray(item) ? flatten(item) : item);
    }, []);
}

const arr3 = [11, [22, 33], [44, [55, 66, [77, [88]], 99]]];
const flatArr3 = flatten(arr3);
console.log(flatArr3); //[11, 22, 33, 44, 55, 66, 77, 88, 99]
// ES6
const arr4 = [1, [2, 3, [4, 5, [6, 7]]]];
ary = arr4.flat(Infinity);

console.log(ary) //[1, 2, 3, 4, 5, 6, 7]

// 方法四：最简单的方法 通过 split 和 toString 两个方法来共同实现数组扁平化
const arr41 = [1, [2, [3, 4]], ["a1", "b1", ["a2", ["cc"]]]];

function flatten(arr) {
    return arr.toString().split(',');
}

console.log(flatten(arr41)); //  ["1", "2", "3", "4", "a1", "b1", "a2", "cc"]

// 自定义深度实现数组的扁平化
function _flat(arr, depth) {
    if(!Array.isArray(arr) || depth <= 0) {
        return arr;
    }
    return arr.reduce((prev, cur) => {
        if (Array.isArray(cur)) {
            return prev.concat(_flat(cur, depth - 1))
        } else {
            return prev.concat(cur);
        }
    }, []);
}

```
### 数组转树形结构
```javascript
let arr = [
  {id: 1, name: '1', pid: 0},
  {id: 2, name: '1-1', pid: 1},
  {id: 3, name: '1-1-1', pid: 2},
  {id: 4, name: '1-2', pid: 1},
  {id: 5, name: '1-2-2', pid: 4},
  {id: 6, name: '1-1-1-1', pid: 3},
  {id: 7, name: '2', pid: 0},
]

function toTree(data, parentId = 0) {
  let itemArr = []
  for (let i = 0; i < data.length; i++) {
    const node = data[i]
    if (node.pid === parentId) {
      const newNode = {
        ...node,
        name: node.name,
        id: node.id,
        children: toTree(data, node.id),
      }
      itemArr.push(newNode)
    }
  }
  return itemArr
}

console.log(toTree(arr))
```
### 数组csv转k-v对象
```javascript
function convertCSVToJSON(str, delimiter = ',') {
  const titles = str.slice(0, str.indexOf('\n')).split(delimiter);
  const rows = str.slice(str.indexOf('\n') + 1).split('\n');
  return rows.map(row => {
      // Convert to 2D array
      const values = row.split(delimiter);
      // Convert array to object
      return titles.reduce((object, curr, i) => {
          object[curr] = values[i];
          return object;
      }, {})
  });
};

let data;

data = 'id, name, email\n0, Todd, todd@gmail.com\n1, Petra, petra@gmail.com';
console.log(convertCSVToJSON(data, ','))
let result = [
  [ 'residence_uuid', 'name', 'email', 'mobile_no', 'ic_number', 'passport_number', 'job_title', 'staff_id', 'office_email' ],
  [ '93e83bfc-0f1d-4c7f-a4ef-0cb9f276c351', 'dfgwer13', 'qbrpko56291@chacuo.net', '60216131344', '', '', 'QA', '37702', '' ],
  [ '' ]
]
// 移除空值
const list = result.filter(element => {
  const arr = element.filter(o => o)
  return arr.length
});
const header = list[0].join(',').replace(/mobile_no/g, 'phone').replace(/department/g, 'block').split(',');
const rows = list.slice(1, list.length)
let data = rows.map(row => {
  return header.reduce((object, curr, i) => (object[curr] = row[i], object), {})
});
```
## 不改变自身的方法
### slice() 拷贝数组中的一部分，返回拷贝后的新数组
```javascript
let arr = [1,2,3,4,5];
let arr1 = arr.slice(1,3); // arr是[1,2,3,4,5]， arr1是[2,3]
let arr2 = arr.slice(-2,-1);  // arr是[1,2,3,4,5], arr2是[4]
```
### join() 将数组转换为字符串，返回字符串
```javascript
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
### toString() 转换数组为字符串，并输出以逗号分隔的字符串列表
```javascript
[1,2,3].toString(); // 1,2,3
[1,[2,'c']].toString(); //1,2,c
```
### concat() 合并两个或多个数组，返回合并后的新数组
```javascript
[1,2,3].concat([4,5,6],[7,8,9]) // [1, 2, 3, 4, 5, 6, 7, 8, 9]
  ['a','b','c'].concat(1,[2,3],[[4,5]]) // ["a", "b", "c", 1, 2, 3, [4,5]]

// concat 和扩展运算符可以快速扁平化数组
const arr = [11, [22, 33], [44, 55], 66];
const flatArr = [].concat(...arr); // [11, 22, 33, 44, 55, 66]
```
## 会改变自身的方法
### push() 往数组的尾部添加一个或多个元素，并返回数组长度
```javascript
let arr = [1,2,3];
let length = arr.push('aa','bb');    // 返回数组长度
console.log(arr,length)
// [1, 2, 3, "aa", "bb"] 5
```
### prop() 删除数组的最后一个元素，并返回删除的值
```javascript
let arr= [1,2,3];
arr.pop()    // 返回删除的值3
```
### unshift() 往数组的头部添加一个或多个元素，并返回数组的长度
```javascript
let arr = [3,4,5];
let length = arr.unshift(1,2);  // 返回长度是5
console.log(arr, length)
//[1, 2, 3, 4, 5] 5
```
### shift() 删除数组的第一个元素,并返回删除的值
```javascript
let arr = [1,2,3];
let item = arr.shift(); // 返回删除的值1
console.log(arr, item)
// [2, 3] 1
```
### splice() 删除数组中的元素，返回原数组删除后的值（亦可在指定位置插入元素）
```javascript
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
### sort() 数组排序，返回排序后的数组
`备注：`sort()函数比较的是ASCII码的大小，而且数组的sort()方法默认是把所有元素先转换为String再排序，所以，直接使用sort()排序，会出现以下问题：
```javascript
// baidu排在了最后:
['Google', 'baidu', 'Facebook'].sort(); // ['Facebook', 'Google", 'baidu']

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
### reverse() 将数组中的元素颠倒顺序，返回逆序的数组
```javascript
let arr = [1,2,3];
arr.reverse()  // arr是[3,2,1]，返回值是[3,2,1]
```
### fill() 用一个固定值填充一个数组中从起始索引到终止索引内的全部元素
```javascript
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


