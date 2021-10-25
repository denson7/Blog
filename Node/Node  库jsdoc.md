[TOC]
## jsdoc
### 安装
```
npm install -g jsdoc
```
### 使用
```
// 生成文件 `input.js` 的说明文档，并存放在 `docs` 目录
jsdoc test.js -d docs
// 使用配置文件config.js生成说明文档, -r 表示遍历文件夹下的子文件夹，配置文件里面 "recurseDepth": 10 代表最多递归10层目录
jsdoc -c config.js -r -d docs
```
config.js配置示例
```
'use strict';
module.exports = {
  "tags": {
    "allowUnknownTags": true
  },
  "source": {
    "include": [ /* array of paths to files to generate documentation for */ ],
    "exclude": [ /* array of paths to exclude */ ],
    "includePattern": ".+\\.js(doc|x)?$",
    "excludePattern": "(^|\\/|\\\\)_"
  },
  "plugins": [],
  "recurseDepth": 10,
  "templates": {
    "cleverLinks": false,
    "monospaceLinks": false,
    "default": {
      "outputSourceFiles": true
    }
  }
}
```
### 常用配置项说明
<table>
    <thead>
    <tr>
        <th>项</th>
        <th>类型</th>
        <th>示例</th>
        <th>说明</th>
    </tr>
    </thead>
    <tbody>
    <tr>
        <td>plugins</td>
        <td><code>Array[String]</code></td>
        <td><code>["plugins/markdown"]</code></td>
        <td>使用插件，附：<a target="_blank" rel="noopener" href="http://usejsdoc.org/about-plugins.html">支持插件</a></td>
    </tr>
    <tr>
        <td>recurseDepth</td>
        <td>int</td>
        <td>10</td>
        <td>文件夹递归深度，只有在命令行加入了 -r 才有用，默认为10</td>
    </tr>
    <tr>
        <td>source</td>
        <td>Object</td>
        <td>参考上面的DEMO</td>
        <td>通过source可以灵活配置需要生成的文件</td>
    </tr>
    <tr>
        <td>type</td>
        <td>String</td>
        <td>支持”module”、”script”</td>
        <td>module（默认）:输入为文件,script:输入为脚本，通常不怎么使用</td>
    </tr>
    <tr>
        <td>opts</td>
        <td>Ojbect</td>
        <td>{“template”:”templates/default”}</td>
        <td>命令行的配置形态，在这里配置一下可以不用在命令行输入了</td>
    </tr>
    <tr>
        <td>opts.template</td>
        <td>string</td>
        <td>“templates/default”</td>
        <td>相当于 -t templates/default</td>
    </tr>
    <tr>
        <td>opts.encoding</td>
        <td>string</td>
        <td>“utf-8”</td>
        <td>相当于 -e utf8</td>
    </tr>
    <tr>
        <td>opts.destination</td>
        <td>string</td>
        <td>“docs”</td>
        <td>相当于 as -d docs</td>
    </tr>
    <tr>
        <td>opts.recurse</td>
        <td>boolean</td>
        <td>true</td>
        <td>相当于 -r</td>
    </tr>
    <tr>
        <td>tags</td>
        <td>Object</td>
        <td>{“allowUnknownTags”: true}</td>
        <td>Tag配置</td>
    </tr>
    <tr>
        <td>templates</td>
        <td>Object</td>
        <td>参考上面的DEMO</td>
        <td>模板配置</td>
    </tr>
    </tbody>
</table>
### Tag
Tag 分为两类块Tag和行Tag，块Tag即是以 @开头的，行Tag是使用大括号{}包起来的, 比如 @param 就是块Tag,{@link}就是行Tag。一般情况下我们使用的都是块Tag。
|功能|Tag|
|:--|:--|
|修饰|@public @private @property @readonly @static @default @global @abstract|
|结构1|@param @return @class @constructs @enum @instance @package @function @constant|
|结构2|@typedef @memberof @module @export @namespace|
|结构3|@requires @override @implements @external|
|文档|@version @author @since @see @deprecated @copyright @description @ignore @license @summary|
#### @type标签
`@type {typeName}`
| 类型 | 说明 |
|---|---|
|string | 字符串 |
|Array or Type[] | 数组 |
|number | 数字 |
|Object | 对象|
|Class | 自定义的类名|
|Function | 方法类型|
|null | -|
|* | 任意类型|
```
/** @type {(string|Array.)} */
var foo;
/** @type {number} */
var bar = 1;

/**
 * @type {number}
 * @const
 */
var FOO = 1;
// 等价于
/** @const {number} */
var FOO = 1;
```
#### @default标签
@default标签描述默认值, 值只能为string|number|boolean|null。
 `@default [<some value>]`
```
const config = {
  /**
   * @default
   */
  enable: true,

  /**
   * @default
   */
  headers: 'x-forwarded-proto',
};
```
#### @param标签
@param标签要求指定要描述参数的名称。还可以包含参数的数据类型，使用大括号括起来，和参数的描述。
`@param {type} {name} {desc}`
```
/**
 * @description 为函数入参定义类型
 * @param {string}         stringVar 字符串
 * @param {Array.<string>} arrayVar  字符串数组
 */
function TagParam(stringVar, arrayVar) {
  //
}
```
#### @returns标签
@returns 标签描述一个函数的返回值。语法和[@param]类似。 
`@return {type} {desc}`
```
/**
 * @description 字符串拼接
 * @param {string} string1
 * @param {string} string2
 * @returns {string}
 */
function concat(string1, string2) {
  return 'hello'
}
```
#### @function标签
@function标记一个对象作为一个函数。
`@function [<FunctionName>]`
```
function paginateFactory(pages) {
  //
}

/** @function */
var paginate = paginateFactory([])
// 如果没有@function标记，paginate对象将被记录为一个一般的对象（一个@member）
```
#### @this标签
@this标签指明this关键字的指向。
`@this <namePath>`
```
class ClassOne {
  test() {
    //
  }
}

class ClassTwo {
  test() {
    // 
  }
}

/**
 * 表示此函数的上下文为 : ClassForThisOne
 * 加入{@this}，迫使"this.test"被描述为"ClassOne#test"，而不是全局的"test()"
 *
 * @function
 * @this {ClassOne}
 */
function FunctionForThis() {
  this.test();
}
```
#### @callback标签
@callback标签提供回调函数的描述，包括回调的参数和返回值。
`@callback <namepath>`
```
/**
 * This callback type is called `doCallback` and is displayed as a global symbol.
 *
 * @callback doCallback
 * @param {string} name
 * @param {number} age
 */
function doCallback(name, age) {
  //
}
/**
 * Does something asynchronously and executes the callback on completion.
 * @param {doCallback} cb - The callback that handles the response.
 */
function doSome(cb) {
  // code
}
```
#### @example标签
@example标签提供一个如何使用描述项的例子。
```
/**
 * Solves equations of the form a * x = b
 * @example <caption>Example usage of method1.</caption>
 * // returns 2
 * method1(5, 10);
 * @example
 * // returns 3
 * method(5, 15);
 * @returns {Number} Returns the value of x for the equation.
 */
const method1 = function (a, b) {
  return b / a;
};
```

#### @readonly标签
@readonly标签标记一个标识符为只读。
```
/**
 * Options for ordering a delicious slice of pie.
 * @namespace
 */
var pieOptions = {
  /**
   * A mode.
   * @readonly
   */
  get mode() {
    return 'dev'
  }
}
```

#### @async标签


## 示例
```
/**
 * @link http://xx.html
 *
 * @description
 */

class ClassJsDoc {
  constructor() {

    /**
     * @type {null}
     */
    this.publicProperty = null;
  }

  /**
   * @description 为函数入参定义类型
   * @param {string} stringVar 字符串
   * @param {Array.<string>} arrayVar 字符串数组
   * @returns {string}
   */
  TagParam1(stringVar, arrayVar) {
    // // FIXME. @param定义arrayVar是{Array.<string>}，所以在push一个{number}类型的变量时，因<类型不符>会产生一个告警
    return ''
  }

  /**
   * @description 如果函数的入参是一个对象，可以定义入参对象属性类型
   * @param {string} people.name 名字
   * @param {number} people.age 年龄
   */
  TagParam2(people) {
    // PASS
    Math.abs(people.age);
    // FIXME. @param定义people.name类型为string，而Math.abs要求入参是number,所以会产生一个告警
    Math.abs(people.name);
  }

  /**
   *
   * @param {Object} obj
   * @constructor
   */
  TagParam3(obj) {
  }

  /**
   * @description 入参默认值
   * @param {Boolean|null} [enable = true] 定义入参enable是可选参数，默认值为true
   */
  TagParamByDefault(enable = true) {
    //
  }
}
```

