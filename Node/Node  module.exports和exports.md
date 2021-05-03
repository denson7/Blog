[TOC]
node在执行一个文件时,会给这个文件内生成一个 exports 对象和一个 module 对象,而这个module 对象又有一个属性叫做 exports.
```node
console.log(exports);
console.log(module);
// 输出
{}
Module {
  id: '.',
    path: 'F:\\Code\\Road\\logs',
    exports: {},
  parent: null,
    filename: 'F:\\Code\\Road\\logs\\buf.js',
    loaded: false,
    children: [],
    paths: [
    'F:\\Code\\Road\\logs\\node_modules',
    'F:\\Code\\Road\\node_modules',
    'F:\\Code\\node_modules',
    'F:\\Code\\node_modules',
    'F:\\node_modules'
  ]
}
```
### module 对当前模块的一个引用
module.exports 用于指定一个模块所导出的内容，即可以通过 require() 访问的内容。如果需要对外导出成员，只需要把导出的成员挂载到module.exports。

### exports 它是对module.exports 的一个引用，只能在模块中引用。
注意：`不能直接赋给exports, 重点，重点，重点`。
```node
const a = 'nihao';
// 错误写法
exports = {a}; // 出差，不能重写exports
// 正确写法
module.export = {a};
exports.foo = 'xxx'
//等价于
module.exports.foo = 'xxx'
```
### 总结
exports 只能使用.语法向外暴露内部变量 例：exports.xxx=xxx
module.exports既可以通过点语法，也可以直接赋值一个对象。module.exports.xxx=xxx 或者 module.exports=xxx
### 示例
```
//
```

