# 字符串常用函数
## 获取字符串长度(含中文)
```js
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
```js
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
```js
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
```js
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
## 删除字符中最后一个被指定的字符
```js
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