##帮助函数
### 模拟手机号
```javascript
//  随机电话号码
function getPhoneNum() {
  const Top3 = [
    '133',
    '149',
    '153',
    '173',
    '177',
    '180',
    '181',
    '189',
    '199',
    '130',
    '131',
    '132',
    '145',
    '155',
    '156',
    '166',
    '171',
    '175',
    '176',
    '185',
    '186',
    '166',
    '134',
    '135',
    '136',
    '137',
    '138',
    '139',
    '147',
    '150',
    '151',
    '152',
    '157',
    '158',
    '159',
    '172',
    '178',
    '182',
    '183',
    '184',
    '187',
    '188',
    '198',
    '170',
    '171',
  ];
  const firstNum = Top3[Math.floor(Math.random() * Top3.length)];
  //随机出剩下的8位数
  let lastNum = '';
  for (let i = 0; i < 8; i++) {
    lastNum += Math.ceil(Math.random() * 10);
  }
  const phoneNum = firstNum + lastNum;
  return phoneNum;
}

```
### 重复执行函数
```javascript
// 传入一个方法，然后每隔一段时间执行一次，执行n次
const repeat = (fn, n, interval) => {
  return (...args) => {
    let counter = 0;
    const timer = setInterval(() => {
      counter++;
      fn.apply(this, args);
      if (counter === n) {
        clearInterval(timer);
      }
    }, interval);
  };
};

const fn = (a, b) => {
  console.log(a, b);
};
//每隔2s输出一次，共输出4次 const repeatFunc = repeat(fn, 4, 2000);
const repeatFn = repeat(fn, 4, 2000);
repeatFn('ni hao-', 123);


```
### 延迟执行，休眠函数
```javascript
// 比如 sleep(1000) 意味着等待1000毫秒，还可从 Promise、Generator、Async/Await 等角度实现。
// Promise
const sleep = time => {
  return new Promise(resolve => setTimeout(resolve, time));
};

sleep(1000).then(() => {
  console.log(1);
});


// Generator
function* sleepGenerator(time) {
  yield new Promise(function(resolve, reject) {
    setTimeout(resolve, time);
  });
}

sleepGenerator(1000)
  .next()
  .value.then(() => {
  console.log(1);
});

//async
function sleep(time) {
  return new Promise(resolve => setTimeout(resolve, time));
}

async function output() {
  let out = await sleep(1000);
  console.log(1);
  return out;
}

output();

function sleep(callback, time) {
  if (typeof callback === "function") {
    setTimeout(callback, time);
  }
}

function output() {
  console.log(1);
}

sleep(output, 1000);
```
### 格式化CSS代码
```javascript
function formatCss(s) {
  // 格式化代码
  s = s.replace(/\s*([\{\}\:\;\,])\s*/g, "$1");
  s = s.replace(/;\s*;/g, ";"); // 清除连续分号
  s = s.replace(/\,[\s\.\#\d]*{/g, "{");
  s = s.replace(/([^\s])\{([^\s])/g, "$1 {\n\t$2");
  s = s.replace(/([^\s])\}([^\n]*)/g, "$1\n}\n$2");
  s = s.replace(/([^\s]);([^\s\}])/g, "$1;\n\t$2");
  return s;
}
```
### 压缩CSS代码
```javascript
function compressCss(s) {
  // 压缩代码
  s = s.replace(/\/\*(.|\n)*?\*\//g, ""); // 删除注释
  s = s.replace(/\s*([\{\}\:\;\,])\s*/g, "$1");
  s = s.replace(/\,[\s\.\#\d]*\{/g, "{"); // 容错处理
  s = s.replace(/;\s*;/g, ";"); // 清除连续分号
  s = s.match(/^\s*(\S+(\s+\S+)*)\s*$/); // 去掉首尾空白
  return s == null ? "" : s[1];
}
```
### 比较版本
```javascript
function compareVersion(v1, v2) {
  v1 = v1.split(".");
  v2 = v2.split(".");

  var len = Math.max(v1.length, v2.length);

  while (v1.length < len) {
    v1.push("0");
  }

  while (v2.length < len) {
    v2.push("0");
  }

  for (var i = 0; i < len; i++) {
    var num1 = parseInt(v1[i]);
    var num2 = parseInt(v2[i]);

    if (num1 > num2) {
      return 1;
    } else if (num1 < num2) {
      return -1;
    }
  }
  return 0;
}
```
### 判断值是否为空(字符串，数组，对象，Map，Set)
```javascript
/**
 * return true if value is not empty
 * @param value
 */
export const isEmpty = (value) => {
  return (
    value === null ||
    value === undefined ||
    (typeof value === 'string' && value === '') ||
    (value.constructor === Object && Object.keys(value).length === 0) ||
    (Array.isArray(value) && value.length === 0) ||
    (value.constructor === Map && value.size === 0) ||
    (value.constructor === Set && value.size === 0)
  );
};
```
### 判断是否是JSON字符串
```javascript
// check is JSON string
export const isJSON = (str: string): boolean => {
  let flag = false;
  if (typeof str == 'string') {
    try {
      const obj = JSON.parse(str);
      if (typeof obj == 'object' && obj) {
        flag = true;
      }
    } catch (e) {
      flag = false;
    }
  }
  return flag;
};
```
### 判断是否是数组
```javascript
/**
 * Determine if a value is an Array
 *
 * @param {Object} val The value to test
 * @returns {boolean} True if value is an Array, otherwise false
 */
export const isArray = (arg: any): boolean => {
  if (typeof Array.isArray === 'undefined') {
    return Object.prototype.toString.call(arg) === '[object Array]';
  }
  return Array.isArray(arg);
};
```
### 判断是否是函数
```javascript
/**
 * Determine if a value is a Function
 *
 * @param {Object} val The value to test
 * @returns {boolean} True if value is a Function, otherwise false
 */
export const isFunction = (val: any): boolean => {
  return Object.prototype.toString.call(val) === '[object Function]';
};
```
### 判断是否是Buffer，二进制，流
```javascript
/**
 * Determine if a value is an Object
 *
 * @param {Object} val The value to test
 * @returns {boolean} True if value is an Object, otherwise false
 */
export const isObject = (val: any): boolean => {
  return val !== null && typeof val === 'object';
};

/**
 * Determine if a value is a Function
 *
 * @param {Object} val The value to test
 * @returns {boolean} True if value is a Function, otherwise false
 */
export const isFunction = (val: any): boolean => {
  return Object.prototype.toString.call(val) === '[object Function]';
};

/**
 * Determine if a value is a Stream
 *
 * @param {Object} val The value to test
 * @returns {boolean} True if value is a Stream, otherwise false
 */
export const isStream = (val: any): boolean => {
  return isObject(val) && isFunction(val.pipe);
};


/**
 * Determine if a value is a Blob
 *
 * @param {Object} val The value to test
 * @returns {boolean} True if value is a Blob, otherwise false
 */
export const isBlob = (val: any): boolean => {
  return Object.prototype.toString.call(val) === '[object Blob]';
};

export const isUndefined = (val: any): boolean => {
  return typeof val === 'undefined';
};

/**
 * Determine if a value is a Buffer
 *
 * @param {Object} val The value to test
 * @returns {boolean} True if value is a Buffer, otherwise false
 */
export const isBuffer = (val: any): boolean => {
  return (
    val !== null &&
    !isUndefined(val) &&
    val.constructor !== null &&
    !isUndefined(val.constructor) &&
    typeof val.constructor.isBuffer === 'function' &&
    val.constructor.isBuffer(val)
  );
};
```
### 判断是否是文件，FormData
```javascript
/**
 * Determine if a value is a File
 *
 * @param {Object} val The value to test
 * @returns {boolean} True if value is a File, otherwise false
 */
export const isFile = (val: any): boolean => {
  return Object.prototype.toString.call(val) === '[object File]';
};

/**
 * Determine if a value is a FormData
 *
 * @param {Object} val The value to test
 * @returns {boolean} True if value is an FormData, otherwise false
 */
export const isFormData = (val: any): boolean => {
  return typeof FormData !== 'undefined' && val instanceof FormData;
};
```
### 判断是否是日期
```javascript
/**
 * Determine if a value is a Date
 *
 * @param {Object} val The value to test
 * @returns {boolean} True if value is a Date, otherwise false
 */
export const isDate = (val: any): boolean => {
  return toString.call(val) === '[object Date]';
};
```
### 判断是否是对象
```javascript
/**
 * Determine if a value is an Object
 *
 * @param {Object} val The value to test
 * @returns {boolean} True if value is an Object, otherwise false
 */
export const isObject = (val: any): boolean => {
  return val !== null && typeof val === 'object';
};
```
### 判断是否是数字，字符串，undefined
```javascript
/**
 * Determine if a value is undefined
 *
 * @param {Object} val The value to test
 * @returns {boolean} True if the value is undefined, otherwise false
 */
export const isUndefined = (val: any): boolean => {
  return typeof val === 'undefined';
};

/**
 * Determine if a value is a String
 *
 * @param {Object} val The value to test
 * @returns {boolean} True if value is a String, otherwise false
 */
export const isString = (val: any): boolean => {
  return typeof val === 'string';
};

/**
 * Determine if a value is a Number
 *
 * @param {Object} val The value to test
 * @returns {boolean} True if value is a Number, otherwise false
 */
export const isNumber = (val: any): boolean => {
  return typeof val === 'number';
};
```
### 转义html特殊字符
```javascript
const escape = str => str.replace(/&/g, '&amp;').replace(/</g, '&lt;').replace(/>/g, '&gt;').replace(/'/g, '&#39;').replace(/"/g, '&quot;');
// Or
const escape = str => str.replace(/[&<>"']/g, m => ({ '&': '&amp;', '<': '&lt;', '>': '&gt;', '"': '&quot;', "'": '&#39;' })[m]);
```
### 显示html特殊字符
```javascript
const unescape = str => str.replace(/&/g , '&').replace(/&lt;/g  , '<').replace(/&gt;/g  , '>').replace(/&#0*39;/g , "'").replace(/&quot;/g, '"');
```
### 重写console.log
```javascript
console.log = (orgin => {
  return function() {
    // 如果是对象 就转成 字符串JSON
    arguments[0] = typeof arguments[0] === "string" ? arguments[0] : JSON.stringify(arguments[0]);
    orgin.call(console, ...arguments);
  };
})(console.log);
```
### 加减乘除精度问题
```javascript
/**
 * 加法函数（精度丢失问题）
 * @param { number } arg1
 * @param { number } arg2
 */
export function add(arg1, arg2) {
  let r1, r2, m;
  try { r1 = arg1.toString().split(".")[1].length } catch (e) { r1 = 0 }
  try { r2 = arg2.toString().split(".")[1].length } catch (e) { r2 = 0 }
  m = Math.pow(10, Math.max(r1, r2));
  return (arg1 * m + arg2 * m) / m
}

/**
 * 减法函数（精度丢失问题）
 * @param { number } arg1
 * @param { number } arg2
 */
export function sub(arg1, arg2) {
  let r1, r2, m, n;
  try { r1 = arg1.toString().split(".")[1].length } catch (e) { r1 = 0 }
  try { r2 = arg2.toString().split(".")[1].length } catch (e) { r2 = 0 }
  m = Math.pow(10, Math.max(r1, r2));
  n = (r1 >= r2) ? r1 : r2;
  return Number(((arg1 * m - arg2 * m) / m).toFixed(n));
}


/**
 * 除法函数（精度丢失问题）
 * @param { number } num1
 * @param { number } num2
 */
export function division(num1,num2){
  let t1,t2,r1,r2;
  try{
    t1 = num1.toString().split('.')[1].length;
  }catch(e){
    t1 = 0;
  }
  try{
    t2=num2.toString().split(".")[1].length;
  }catch(e){
    t2=0;
  }
  r1=Number(num1.toString().replace(".",""));
  r2=Number(num2.toString().replace(".",""));
  return (r1/r2)*Math.pow(10,t2-t1);
}

/**
 * 乘法函数（精度丢失问题）
 * @param { number } num1
 * @param { number } num2
 */
export function mcl(num1,num2){
  let m=0,s1=num1.toString(),s2=num2.toString();
  try{m+=s1.split(".")[1].length}catch(e){}
  try{m+=s2.split(".")[1].length}catch(e){}
  return Number(s1.replace(".",""))*Number(s2.replace(".",""))/Math.pow(10,m);
}
```
### 检查元素是否具有指定的类
```javascript
// DOM里的每个节点上都有一个classList对象,可以新增、删除、修改节点上的CSS类
const hasClass = (el, className) => el.classList.contains(className);

// 事例
hasClass(document.querySelector('p.special'), 'special'); // true
```
### 判断父元素是否包含子元素
```javascript
const elementContains = (parent, child) => parent !== child && parent.contains(child);

// 事例
elementContains(document.querySelector('head'), document.querySelector('title'));
// true
elementContains(document.querySelector('body'), document.querySelector('body'));
// false
```
### 获取元素中所有的图片
```javascript
const getImages = (el, includeDuplicates = false) => {
  const images = [...el.getElementsByTagName('img')].map(img => img.getAttribute('src'));
  return includeDuplicates ? images : [...new Set(images)];
};

// 事例：includeDuplicates 为 true 表示需要排除重复元素
getImages(document, true); // ['image1.jpg', 'image2.png', 'image1.png', '...']
getImages(document, false); // ['image1.jpg', 'image2.png', '...']
```

### 在等待指定时间后调用某个函数
```javascript
const delay = (fn, wait, ...args) => setTimeout(fn, wait, ...args);
delay(
  function(text) {
    console.log(text);
  },
  1000,
  'later'
);
// 1秒后打印 'later'
```
### 从元素中移除事件监听器
```javascript
// 从元素中移除事件监听器
const off = (el, evt, fn, opts = false) => el.removeEventListener(evt, fn, opts);

const fn = () => console.log('!');
document.body.addEventListener('click', fn);
off(document.body, 'click', fn);
```
### 判断浏览器标签页是都聚焦
```javascript
// 判断当前浏览器标签页是否活跃
const isBrowserTabFocused = () => !document.hidden;
// 事例
isBrowserTabFocused(); // true
```
### 数字千分化
```javascript
const ThousandNum = (num = 0) => {
  const str = (+num).toString().split(".");
  const int = nums => nums.split("").reverse().reduceRight((t, v, i) => t + (i % 3 ? v : `${v},`), "").replace(/^,|,$/g, "");
  const dec = nums => nums.split("").reduce((t, v, i) => t + ((i + 1) % 3 ? v : `${v},`), "").replace(/^,|,$/g, "");
  return str.length > 1 ? `${int(str[0])}.${dec(str[1])}` : int(str[0]);
};

ThousandNum(1234); // "1,234"
ThousandNum(1234.00); // "1,234"
ThousandNum(0.1234); // "0.123,4"
ThousandNum(1234.5678); // "1,234.567,8"
```
### 格式化金钱数字
```javascript
// 方法1
var test1 = '1234567890';
var format = test1.replace(/\B(?=(\d{3})+(?!\d))/g, ',');
console.log(format); // 1,234,567,890
const FormatMoney = num => num.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ',');
FormatMoney(10000000012); // "10,000,000,012"
// 方法2
function formatCash(str) { // 字符串
  return str.split('').reverse().reduce((prev, next, index) => {
    return ((index % 3) ? next : (next + ',')) + prev
  })
}
```
### 取模
```javascript
// 由于偶数的最低位为 0，奇数为 1，所以取模运算可以用位操作来代替。
const value = 121;
if (value % 2) {
  // 奇数
} else {
  // 偶数
}

// 使用位运算
if (value & 1) {
  // 奇数
} else {
  // 偶数
}
```
### 取整
```javascript
var a = ~~2.33;   // 2
var b = 2.33 | 0;  // 2
var c = 2.33 >> 0; // 2
```

### switch/case另一种写法
```javascript
var process = function(){
  console.log('执行')
};
switch (value)
{
  case 0:
    process();
    break;
  case 1:
    process();
    break;
  case 2:
    process();
    break;
  case 3:
    process();
    break;
}

//将判断条件作为对象的属性名，将处理逻辑作为对象的属性值
var process = function () {
  console.log();
};

const MapArr = new Map([
  [0, process],
  [1, process],
  [2, process],
  [3, process]
]);

let getFunction = function (val) {
  return MapArr.get(val)
};

let getMap = getFunction(1); //如果查找不到返回undefined
if (!getMap) {
  console.log('查找不到');
} else {
  console.log('执行操作');
  getMap()
}
```
### 在给定范围内生成随机整数数组
```javascript
const randomArrayInRange = (min, max, n) => Array.from({ length: n }, () => Math.floor(Math.random() * (max - min + 1)) + min);

// Example
randomArrayInRange(1, 100, 10);     
// [11, 82, 41, 35, 76, 83, 43, 15, 60, 54]
```
### 随机生成一个uuid
```javascript
// 方法1
// @ts-ignore
const uuid = (a) => a ? (a ^ Math.random() * 16 >> a / 4).toString(16) : ([1e7] + -1e3 + -4e3 + -8e3 + -1e11).replace(/[018]/g, uuid);
// @ts-ignore
uuid(); // "1afd5060-62d7-4a4c-aef4-4015fc558447"

// 方法2
const generateUUID = () => {
  return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function (c) {
    const r = Math.random() * 16 | 0;
    const v = c === 'x' ? r : ((r & 0x3) | 0x8);
    return v.toString(16);
  });
}
generateUUID(); // "112e5cef-05ec-4948-b20b-5c0897e77157"
```
### 生产一个随机字符串
```javascript
/**
 * 生成一个随机字符串，长度为num
 * @param num
 * @return {string}
 */
export const randomString = (num) => {
  let str = "";
  // Date.now().toString(36) // 生成8为随机数; Math.random().toString(36).substring(2) 生成10位随机数
  const chars = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";
  for (let i = 0; i < num; i++)
    str += chars.charAt(Math.floor(Math.random() * chars.length));
  return str;
}

```
```
const generateString = length => Array(length).fill('').map((v) => Math.random().toString(36).charAt(2)).join('');
generateString(4); // 'asdw'
```
### 生成一个指定范围的随机整数
```javascript
/**
 * 生成一个指定范围的随机数
 * @param { number } min
 * @param { number } max
 */
export const RandomNum = (min, max) => Math.floor(Math.random() * (max - min + 1)) + min;
```

### 字节Byte转换K或M
```javascript
// 字节转K或M
export const getDiskSize = (bytes) => {
    if(bytes === 0) {
        return '0B';
    }
    const k = 1024;
    const sizes = ['B','KB', 'MB', 'GB', 'TB', 'PB', 'EB', 'ZB', 'YB'];
    const i = Math.floor(Math.log(bytes) / Math.log(k));
 return parseFloat((bytes / Math.pow(k, i)) . toFixed(2)) + '' + sizes[i];
}
```
### 获取url的参数
```javascript
export const getQueryStringArgs = (url) =>  {
  let args = {};
  // 判断是否有?
  if (url && url.indexOf('?') > -1) {
    // 根据?拆分url
    const arr = url.split('?');
    const qs = arr[1];
    const items = qs.length ? qs.split('&') : [];
    let item = null, name = null, value = null;
    for (let i = 0; i < items.length; i++) {
      item = items[i].split('=');
      name = decodeURIComponent(item[0]);
      value = decodeURIComponent(item[1]);
      if (name.length) {
        args[name] = value;
      }
    }
  }
  return args;
};
```
### 从URL中获取某一个参数
```javascript
// 获取url参数
export function getUrlParam(name) {
    const reg = new RegExp('(^|&)' + name + '=([^&]*)(&|$)');
    const res = window.location.search.substr(1).match(reg);
    if (res != null) return _domXssSafe(res[2]); return '';
}

function _domXssSafe(str) {
    return str.replace(/</g, '&lt;').replace(/>/g, '&gt;').replace(/'/g, '&apos;').replace(/"/g, '&quot;');
}
```
### 模拟form表单提交
```javascript
export const formSender = (path, params, method, target) => {
    let _method = method || "post";
    let _target = target || "_self";
    let form = document.createElement("form");
    form.setAttribute("method", _method);
    form.setAttribute("action", path);
    form.setAttribute("target", _target);
    form.style.display = "none";

    for (let key in params) {
        if (params.hasOwnProperty(key)) {
            if (Object.prototype.toString.apply(params[key]) !== "[object Array]") {
                let hiddenField = document.createElement("input");
                hiddenField.setAttribute("type", "hidden");
                hiddenField.setAttribute("name", key);
                hiddenField.setAttribute("value", params[key]);
                form.appendChild(hiddenField);
            } else {
                for (var index in params[key]) {
                    let hiddenField = document.createElement("input");
                    hiddenField.setAttribute("type", "hidden");
                    hiddenField.setAttribute("name", key);
                    hiddenField.setAttribute("value", params[key][index]);
                    form.appendChild(hiddenField);
                }
            }
        }
    }

    document.body.appendChild(form);
    form.submit();
    setTimeout(function () {
        document.body.removeChild(form);
    }, 2000);
};
// 使用示例
formSender('https://buy.' + document.domain + '/order/check', {
    "itemDetails": JSON.stringify({
        "raw_goodsData": resp.data.param.goods,
        "extParams": {
            "secretId": resp.data.param.auth.secretId,
            "signature": resp.data.param.auth.signature,
            "timestamp": resp.data.param.auth.timestamp,
            "version": resp.data.param.auth.version // 添加
        }
    })
}, 'post', '_self');
```
### 自动跳转
```javascript
export const pageRedirect = (url, timeout = 0) => {
  // 内嵌
  if (window.top != window.self) {
    setTimeout(() => { window.top.location.href = url; }, timeout);
  } else {
    setTimeout(() => { window.location.href = url; }, timeout);
  }
};
```
### console美化输出
```javascript
console.info("%c哈哈", "color: #3190e8; font-size: 30px; font-family: sans-serif");
```
### 异步累计
```javascript
async function AsyncTotal(arr = []) {
  return arr.reduce(async(t, v) => {
    const at = await t;
    const todo = await Todo(v);
    at[v] = todo;
    return at;
  }, Promise.resolve({}));
}
// 
const result = await AsyncTotal(); // 需要在async包围下使用
```
### 产生随机十六进制颜色
```javascript
const randomColor = () => `#${Math.random().toString(16).slice(2, 8).padEnd(6, '0')}`;
// Or
const randomColor = () => `#${(~~(Math.random()*(1<<24))).toString(16)}`;
// "#f145cb"
```
### hex to rgb
```javascript
const hexToRgb = (hex) =>
  hex
    .replace(
      /^#?([a-f\d])([a-f\d])([a-f\d])$/i,
      (_, r, g, b) => `#${r}${r}${g}${g}${b}${b}`
    )
    .substring(1)
    .match(/.{2}/g)
    .map((x) => parseInt(x, 16));

// Examples
hexToRgb("#00ffff"); // [0, 255, 255]
hexToRgb("#0ff"); // [0, 255, 255]
```
### rgb to hex
```javascript
const rgbToHex = (red, green, blue) =>
  `#${((1 << 24) + (red << 16) + (green << 8) + blue).toString(16).slice(1)}`;

// Or
const rgbToHex = (red, green, blue) =>
  `#${[red, green, blue].map((v) => v.toString(16).padStart(2, "0")).join("")}`;

// Example
rgbToHex(0, 255, 255); // '#00ffff'
```
### http 重定向到https
```javascript
const redirectHttps = () =>
  location.protocol === "https:"
    ? {}
    : location.replace(`https://${location.href.split("//")[1]}`);

// Or
const redirectHttps = () =>
  location.protocol === "https:" ? {} : (location.protocol = "https:");
```
### JSON数据转URL参数
```javascript
/**
 * {a:1, b:2} -> a=1&b=2
 * @param params
 */
export const convertParamsToUrlQuery = (params) => {
  let types = Object.prototype.toString.call(params).slice(8, -1);
  if (types === 'Object') {
    let tempArr = [];
    for (let i in params) {
      let key = encodeURIComponent(i);
      let value = encodeURIComponent(params[i]);
      tempArr.push(key + '=' + value);
    }
    let urlParamsStr = tempArr.join('&');
    return urlParamsStr;
  } else {
    throw 'model is not object';
  }
};
```
