[TOC]
## 安装

```javascript
npm install -g typescript
// 安装完成之后，我们就可以在任何地方执行 tsc 命令
tsc -V  // Version 3.5.3
// 编译ts文件
tsc hello.ts // 执行改命令会生成一个编译好的hello.js文件
// 注意: TypeScript 只会进行静态检查，如果发现有错误，编译的时候就会报错。
// 但是，TypeScript 编译的时候即使报错了，还是会生成编译结果。如果要在报错的时候终止 js 文件的生成，可以在tsconfig.json中配置noEmitOnError 设置为true，即可。
```
## 升级
```
$ npm -g upgrade typescript
```
##配置tsconfig.json
```javascript
##VSCode自动编译
1.第一步，执行命令：tsc --init 生成tsconfig.json文件，修改配置项 "outDir": "./output"
2.第二步，VSCode 选择Terminal ->Run Task -> 选择 tsc: watch - Web/js/tsconfig.json文件即可。
https://www.tslang.cn/docs/handbook/compiler-options.html
```




