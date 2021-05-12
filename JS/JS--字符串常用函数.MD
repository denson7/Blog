# 字符串常用函数
### 检查路径是否是相对路径
```javascript
const isRelative = path => !/^([a-z]+:)?[\\/]/i.test(path);

// Examples
isRelative('/foo/bar/baz');         // false
isRelative('C:\\foo\\bar\\baz');    // false
isRelative('foo/bar/baz.txt');      // true
isRelative('foo.md');               // true
```
### 检查路径是否是绝对路径
```javascript
const isAbsoluteUrl = url => /^[a-z][a-z0-9+.-]*:/.test(url);

// Example
isAbsoluteUrl('https://1loc.dev');          // true
isAbsoluteUrl('https://1loc.dev/foo/bar');  // true
isAbsoluteUrl('1loc.dev');                  // false
isAbsoluteUrl('//1loc.dev');                // false
```
### 检查字符串是否为回文
```javascript
const isPalindrome = str => str === str.split('').reverse().join('');

// Examples
isPalindrome('abc');        // false
isPalindrom('abcba');       // true
```
### 将字符串转换为驼峰命名
```javascript
const toCamelCase = str => str.trim().replace(/[-_\s]+(.)?/g, (_, c) => c ? c.toUpperCase() : '');

// Examples
toCamelCase('background-color');            // backgroundColor
toCamelCase('-webkit-scrollbar-thumb');     // WebkitScrollbarThumb
toCamelCase('_hello_world');                // HelloWorld
toCamelCase('hello_world');                 // helloWorld
```
### 将字符串转换为大驼峰
```javascript
const toPascalCase = str => (str.match(/[a-zA-Z0-9]+/g) || []).map(w => `${w.charAt(0).toUpperCase()}${w.slice(1)}`).join('');

// Examples
toPascalCase('hello world');    // 'HelloWorld'
toPascalCase('hello.world');    // 'HelloWorld'
toPascalCase('foo_bar-baz');    // FooBarBaz
```
### 小驼峰命名转下划线命名
```javascript
const toLineName = (name) => name.replace(/([A-Z])/g,"_$1").toLowerCase();
```
### 下划线命名转驼峰命名
```javascript
function toHump(name) {
  return name.replace(/\_(\w)/g, function(all, letter){
      return letter.toUpperCase();
  });
}
```
### 将windows文件路径转换为unix路径
```javascript
const toUnixPath = path => path.replace(/[\\/]+/g, '/').replace(/^([a-zA-Z]+:|\.\/)/, '');

// Examples
toUnixPath('./foo/bar/baz');        // foo/bar/baz
toUnixPath('C:\\foo\\bar\\baz');    // /foo/bar/baz
```
## 截断指定长度的字符串
```javascript
const truncate = (str, max, suffix) => str.length < max ? str : `${str.substr(0, str.substr(0, max - suffix.length).lastIndexOf(' '))}${suffix}`;
// Example
truncate('This is a long message', 20, '...');  
// 'This is a long...'
```



## 字符串转数组
```javascript
// 方法一
console.log(Object.values('abc')); // ["a", "b", "c"]
// 方法二
console.log(Array.from('abcd')); // ["a", "b", "c", "d"]
// 方法三(常用)
console.log('abcd'.split('')); // ["a", "b", "c", "d"]
// 方法四
console.log([].slice.call('abcd')); // ["a", "b", "c", "d"]
// 方法五，使用 Reflect.apply 改变 slice 函数指向
console.log(Reflect.apply([].slice, 'abcd', [])); // ["a", "b", "c", "d"]
```
## 获取字符串长度(含中文)
```javascript
// 方法一
getStringLength = (str) => {
  // 定义字符串长度
  let realLength = 0
  // 定义字符串相应位置字符的编码值
  let charCode = 0
  for(let i = 0; i < str.length; i++) {
    // 获取字符的unicode码
    charCode = str.charCodeAt(i)
    // 判断unicode
    if (charCode >=0 && charCode <= 127) {
      // 普通字符占一个字符
      realLength += 1
    } else {
      // 中文字符占两个字符
      realLength += 2
    }
  }
  return realLength
}

// 方法二
getStringLength = (str) => {
  // 先把中文替换成两个字节的英文，在计算长度
  return str.replace(/[\u0391-\uFFE5]/g,"aa").length;
}
```
## 字符串去重
```javascript
// 字符串去重
uniqueString = (str) => {
  const res = []
  const arr = str.split("")
  for (let i = 0; i < arr.length; i++) {
    if (res.indexOf(arr[i]) === -1) {
      res.push(arr[i])
    }
  }
  return res.join("")
}
```
## 获取字符串中字符出现的次数
```javascript
// 获取字符串中每个字符出现的次数
getCountNum = (str) => {
  const res = []
  const unique = []
  let count = 0
  const arr = str.split("")
  // 获取去重后的字符
  for (let i = 0; i < arr.length; i++) {
    if(unique.indexOf(arr[i]) === -1){
      unique.push(arr[i])
    }
  }
  // 对去重后的数组遍历，分别与原始数组中每个值进行比较，如果相等则count++
  for (let i = 0; i < unique.length; i++) {
    for(let j = 0; j < arr.length; j++){
      if(unique[i] === arr[j]){
        count++
      }
    }
    res.push({name:unique[i], count: count})
    count = 0
  }
  return res
}
```
## 获取最长的子串
```javascript
lengthOfLongestSubstring = (s) => {
  // 用于存放当前最长无重复子串的长度
  let res = 0;
  // 用于存放无重复子串
  let str = "";
  let len = s.length;
  for(let i = 0; i < len; i++) {
    // debugger
    let char = s.charAt(i);
    let index = str.indexOf(char);
    if(index === -1) {
      str += char;
      res = res < str.length ? str.length : res;
    } else {
      str = str.substr(index + 1) + char;
    }
  }
  const obj = {str:str, len:res};
  return obj;
};
```
## 去除首尾指定字符
```javascript
/**
 * 
 * @param {*} str 
 * @param {*} val 
 * @param {*} type
 * eg:
  let str = ',abc,cde,fff,'; // 删除首尾的,号
  str = str.replace(/^(\s|,)+|(\s|,)+$/g, ''); 
 */
export const strTrim = (str, val = '', type = '') => {
  // return str.replace(new RegExp('^'+val+'+|'+val+'+$','g'), '');
  if (val) {
    if (type === 'left') {
      // 删除左侧字符
      return str.replace(new RegExp('^\\'+val+'+', 'g'), '');
    } else if (type === 'right') {
      // 删除右侧字符
      return str.replace(new RegExp('\\'+val+'+$', 'g'), '');
    }
    return str.replace(new RegExp('^\\'+val+'+|\\'+val+'+$', 'g'), '');
  } else {
    return str.replace(/^\s+|\s+$/g, '')
  }
}
```
## 删除字符中最后一个被指定的字符
```javascript
// 删除字符串中最后一个被指定的字符(单个字符)
// 方法一
delLastString = (str, target) => {
    let reg = new RegExp(`${target}(?=([^${target}]*)$)`)
    return str.replace(reg, '')
}

// 方法二
delLastString = (str, target) => {
    if (typeof str === 'string') {
        let index = str.lastIndexOf(target);
        return str.substring(0, index) + str.substring(index + 1, str.length);
    }
}
// 方法三
delLastString = (str, target) => {
    return str.split('').reverse().join('').replace(target,"").split("").reverse().join("")
}
```
## 去除字符串中的空格
```javascript
/**
 * @description 去除空格
 * @param { string } str 待处理字符串
 * @param  { number } type 去除空格类型 1-所有空格  2-前后空格  3-前空格 4-后空格 默认为1
 */
export const trim = (str, type = 1) => {
  if (type && type !== 1 && type !== 2 && type !== 3 && type !== 4) return;
  switch (type) {
    case 1:
      return str.replace(/\s/g, '');
    case 2:
      return str.replace(/(^\s)|(\s*$)/g, '');
    case 3:
      return str.replace(/(^\s)/g, '');
    case 4:
      return str.replace(/(\s$)/g, '');
    default:
      return str;
  }
};
```