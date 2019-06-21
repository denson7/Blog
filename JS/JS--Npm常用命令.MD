##npm常用命令
###npm install 安装
```
//默认会安装最新的版本
//全局安装
$ npm install -g webpack
//本地安装
$ npm install webpack
//指定版本安装
$ npm install webpack@4.8.3
//查看帮助
$ npm install --help
npm install (with no args, in package dir)
npm install [<@scope>/]<pkg>
npm install [<@scope>/]<pkg>@<tag>
npm install [<@scope>/]<pkg>@<version>
npm install [<@scope>/]<pkg>@<version range>
npm install <folder>
npm install <tarball file>
npm install <tarball url>
npm install <git:// url>
npm install <github username>/<github project>

aliases: i, isntall, add
common options: [--save-prod|--save-dev|--save-optional] [--save-exact] [--no-save]
```
```
#-S, --save 安装包信息将加入到package.json文件中的dependencies（生产阶段的依赖）
$ npm install --save webpack
//等价于
$ npm install -S webpack
```
```
#-D, --save-dev 安装包信息将加入到devDependencies（开发阶段的依赖），一般开发阶段使用
$ npm install --save-dev webpack
//等价于
$ npm install -D webpack
```
```
#-O, --save-optional 安装包信息将加入到optionalDependencies（可选阶段的依赖）
$ npm install --save-optional webpack
//等价于
$ npm install -O webpack
```

###npm uninstall 卸载
```
$ npm uninstall --help
npm uninstall [<@scope>/]<pkg>[@<version>]... [--save-prod|--save-dev|--save-optional] [--no-save]

aliases: un, unlink, remove, rm, r
//示例，卸载开发版本的模块
$ npm uninstall webpack --save-dev
```
###更新
```
//检查
$ npm install -g npm-check
$ npm-check
//更新
$ npm install -g npm-upgrade
$ npm-upgrade
```
###检查是否过期
```
//列出所有已经过时了的模块
$ npm outdated 
```
###初始化
```
$ npm init
$ npm init -y
```
###npm run 启动模块
```
//该命令写在package.json文件scripts的start字段中,可以自定义字段用于启动。
  "scripts": {
    "start":"webpack",
    "test": "echo \"Error: no test specified\" && exit 1"
  },

$ npm run test
```
###npm version 查看版本
```
$ npm -v
5.6.0
$ npm version
{ '09-04': '1.0.0',
  npm: '5.6.0',
  ares: '1.10.1-DEV',
  cldr: '32.0',
  http_parser: '2.7.0',
  icu: '60.1',
  modules: '57',
  nghttp2: '1.25.0',
  node: '8.10.0',
  openssl: '1.0.2n',
  tz: '2017c',
  unicode: '10.0',
  uv: '1.19.1',
  v8: '6.2.414.50',
  zlib: '1.2.11' }
```
### npm config 管理npm配置
```
$ npm config --help
npm config set <key> <value>
npm config get [<key>]
npm config delete <key>
npm config list [--json]
npm config edit
npm set <key> <value>
npm get [<key>]

alias: c

//示例
$ npm config list --json
//临时修改npm镜像
$ npm config set registry="https://registry.npm.taobao.org"
```
###npm root 查看包的安装路径
```
//查看 node_modules的路径。
//查看全局
$ npm root -g
C:\Users\Administrator\AppData\Roaming\npm\node_modules
//查看本地
$ npm root
F:\Code\Tencent\learnReact\09-07\node_modules
```
###查看帮助
```
//会在浏览器打开本地的帮助文件
$ npm help install
```


