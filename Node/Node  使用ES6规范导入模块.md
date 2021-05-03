[TOC]
## 使用import 和export 
### 方法1 使用自身
Node 有一个尚处于 Experimental 阶段的模块，让我们可以抛弃 babel 等一类工具的束缚，直接在 Node 环境下使用 import/export。
```
注意：
Node版本需大于 9.0 
使用 import/export 的文件后缀名必须为 .mjs
```
示例
```
//main.mjs
// const fs = require('fs');
import fs from 'fs';
console.log(fs);
//执行
node --experimental-modules ./main.mjs
```
### 方法2 使用babel
安装必要包
```
npm install --save-dev @babel/core @babel/cli @babel/preset-env @babel/plugin-proposal-class-properties rimraf  nodemon
```
在项目根目录新建.babelrc文件，添加如下内容
```
{
  "presets": ["@babel/preset-env"],
  "plugins": ["@babel/plugin-proposal-class-properties"]
}
```
修改package.json文件
```
  "scripts": {
    "build": "babel ./src -d dist", //默认是dist目录的index.js文件
    "start": "npm run build && node dist",
    "restart": "rimraf dist && npm run start",
    "dev": "nodemon --exec npm run restart"
  },
```
在根目录新建nodemon.json文件，添加如下内容
```
{ "watch": ["src"] }
```
现在就可以实时监控文件变化了。

### 方法3 使用 babel-register
注意 babel的版本<7
安装包
```
npm install babel-register babel-preset-env -D
```
修改文件，使用import替换require
```
//main.js
// const fs = require('fs');
import fs from 'fs';
console.log(fs);
```
这里需要新建一个index.js，最后运行node index.js这个文件，即可。
```
require('babel-register')({
  presets: ['env'],
});
module.exports = require('./main.js');
```
