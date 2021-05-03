[TOC]
## CommonJS
Node.js中用到的模块导入导出都是依照CommonJS标准来实现的。
```
// 导出
// B.js
// 定义了函数show
function show() {
    console.log('show方法被调用')
}

// 定义了变量count
let count = 3

/*--------------  导出方法  --------------*/

// 第一种
module.exports = {
    show,
    count
}

// 第二种
exports.show = show
exports.count = count

// 第一种导出方式是将需要导出的函数或变量存储到 module.exports 里面，其中 module.exports 原本是一个空对象。
// 第二种导出方式中，exports 在内部其实是指向了 module.exports，所以当我们执行 exports.变量 或 exports.函数 时，其实就相当于把变量或函数存储到 module.exports 中。
// 注意：在使用第二种导出时，不能对 exports 进行重新赋值，否则就将 module.exports 直接全部覆盖了。

//  导入
// A.js
const bModule = require('./B.js')

console.log(bModule.count)  // 3

bModule.show()  // show方法被调用
```
## ES6 Module
```
// B.js
// 单个变量或函数导出
export function show() { console.log('show方法被调用') }
export let count = 3

// 批量导出
function show() { console.log('show方法被调用') }
let count = 3
export {show, count}

// 默认导出
export default show

// 导入
import {show, count} from './B.js'
import {show as print, count as number} from './B.js'
import * as bModule from './B.js'
```

## 区别
### 1.对于模块的依赖，CommonJS是动态的，ES6 Module 是静态的。
```
动态是指对于模块的依赖关系建立在代码执行阶段；
静态是指对于模块的依赖关系建立在代码编译阶段；
```
### 2.CommonJS导入的是值的拷贝，ES6 Module导入的是值的引用
验证CommonJS
```
// B.js
let count = 3

function change() {
    count ++    // 变量count + 1
    console.log('原count值为：', count);  // 打印B.js模块中count的值
}

module.exports = {
    count,
    change
}

// A.js
let count = require('./B.js').count 
let change = require('./B.js').change

console.log('改变前：', count);   
change()     // 调用模块B.js中的change方法，将原来的count + 1
console.log('改变后：', count); 

// 运行A.js文件的结果
改变前：3
原count值为：4
改变后：3
```
验证ES6 Module
```
// B.js
let count = 3

function change() {
    count ++        // 变量count + 1
    console.log(count);   // 打印B.js模块中count的值
}

export {count, change}

// A.js
import {count, change} from './B.js';

console.log('改变前：',count);

change()         // 调用模块B.js中的change方法，将原来的count + 1

console.log('改变后：', count);

// 运行A.js文件的结果
改变前：3
原count值为：4
改变后：4
```
