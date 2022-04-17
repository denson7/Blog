[TOC]
```javascript
// 参考文档
https://ts.xcatliu.com/basics/declaration-files
```
## 类型定义文件
在TS中，类型定义文件以`xx.d.ts`文件结尾，是用来帮助TS理解JS文件。
### 关键字 declare
一般写在类的定义/描述文件xx.d.ts中，表示声明作用。它可以用来声明变量(var/let/const)、方法(function)、命名空间(namespace)、类(class)、接口(interface)、模块(module)。
```javascript
declare var 声明全局变量
declare function 声明全局方法
declare class 声明全局类
declare enum 声明全局枚举类型
declare namespace 声明（含有子属性的）全局对象
declare global 扩展全局变量
declare module 扩展模块
  
interface 和 type 声明全局类型
export 导出变量
export namespace 导出（含有子属性的）对象
export default ES6 默认导出
export = commonjs 导出模块
export as namespace UMD 库声明全局变量

/// <reference /> 三斜线指令
```
### 书写声明文件
```javascript
// xxx.d.ts 类型定义文件,帮助TS理解JS文件

/**
 $(function () {
  $("body").html("<div>222</div");
  new $.fn.init();
});
 */
// 定义全局变量
// declare var $: (param: () => void) => void;

interface JqueryInstance {
  html: (html: string) => JqueryInstance;
}
// 定义全局函数
// 函数重载的方式
declare function $(func: () => void): void;
declare function $(selector: string): JqueryInstance;

// 如何对一个对象进行类型定义，以及如何对一个类进行类型定义，以及命名空间的嵌套
// 使用 new $.fn.init();
declare namespace $ {
  namespace fn {
    // function init1(): v oid;
    class init {}
  }
}

// 使用接口的方式写函数重载
// interface JQuery {
//   (func: () => void): void;
//   (selector: string): JqueryInstance;
// }
// declare var $: JQuery;
```
### 第三方声明文件
当使用第三方库时，需要引用他的声明文件，这样就能获得对应的代码补全、接口提示等功能。
```
npm install @type/第三方库 --save-dev 
```
### 模块化
```javascript
// ES6 模块化
/**
 import jquery from "jquery";
 
 $(function () {
  $("body").html("<div>222</div");
  new $.fn.init();
});
 */
declare module "jquery" {
  interface JqueryInstance {
    html: (html: string) => JqueryInstance;
  }
  // 混合类型
  function $(func: () => void): void;
  function $(selector: string): JqueryInstance;
  namespace $ {
    namespace fn {
      // function init1(): v oid;
      class init {}
    }
  }

  // 导出
  export = $;
}
```

### 示例: 扩展express类型定义文件
```javascript
import { Router, Request, Response } from 'express';

interface RequestWithBody extends Request {
  body: {
    [key: string]: string | undefined;
  };
}

const router = Router();

// RequestWithBody 扩展 Request
router.post('/getData', (req: RequestWithBody, res: Response) => {
  const { password } = req.body;
  if (password === '123') {
    res.send('getData Success!');
  } else {
    res.send(`Error!`);
  }
});

export default router;
```

```javascript
interface Result {
  sucesss: boolean;
  errMsg?: string;
  data: any;
}

export const getResponseData = (data: any, errMsg?: string): Result => {
  if (errMsg) {
    return {
      sucesss: false,
      errMsg,
      data
    };
  }
  return {
    sucesss: true,
    data
  };
};

```
### 引入图片
```javascript
Error: cannot find module xxx.gif 
// 解决办法: 新建typings.d.ts文件
//style
declare module '*.css'
declare module '*.less'
declare module '*.scss'

//图片
declare module "*.svg";
declare module "*.png";
declare module "*.jpg";
declare module "*.jpeg";
declare module "*.gif";
declare module "*.bmp";
declare module "*.tiff";
```
### 声明文件被识别
声明文件必需以 .d.ts 为后缀。
一般来说，ts 会解析项目中所有的 *.ts 文件，当然也包含以 .d.ts 结尾的文件。假如没有解析，那么可以检查下 tsconfig.json 中的 files、include 和 exclude 配置，确保其包含了 声明的xx.d.ts 文件。
注意：一般手动写的声明文件，那么需要满足以下条件之一，才能被正确的识别：
1.给 package.json 中的 types 或 typings 字段指定一个类型声明文件地址
2.在项目根目录下，编写一个 index.d.ts 文件
3.针对入口文件（package.json 中的 main 字段指定的入口文件），编写一个同名不同后缀的 .d.ts 文件.

示例：
例如：给 package.json 中的 types 或 typings 字段指定一个类型声明文件地址。比如：
```javascript
{
    "name": "foo",
    "version": "1.0.0",
    "main": "lib/index.js",
    "types": "foo.d.ts", // (typings 与 types 一样，只是另一种写法)
}
```
如果package.json没有指定 types 或 typings，那么就会在根目录下寻找 index.d.ts 文件，将它视为此库的类型声明文件。
如果也没有找到 index.d.ts 文件，那么就会寻找入口文件（package.json 中的 main 字段指定的入口文件）是否存在对应同名不同后缀的 .d.ts 文件。

```javascript
// tsconfig.json
typeRoots 属性表示只有typeRoots下面的包才会被包含进来。
https://www.tslang.cn/docs/handbook/tsconfig-json.html
```

