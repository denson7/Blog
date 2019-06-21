## Yarn常用命令
### 安装
```
// 全局安装
npm install -g yarn
// 查看版本
yarn -V
// 查看帮助
yarn -h
// 查看镜像
yarn config get registry
// 更改镜像源
yarn config set registry https://registry.npm.taobao.org
```

### 与npm对比使用

|Npm|Yarn|命令说明|
|--|--|
|npm init|yarn init|初始化包的开发环境|
|npm install|yarn install|安装package文件里定义的所有依赖|
|npm install --save xxx|yarn add xxx|安装某个依赖，默认保存到package中|
|npm uninstall --save xxx|yarn remove xxx|移除某个依赖项目|
|npm install --save-dev xxx|yarn add --dev xxx|安装某个开发时依赖项目|
|npm update --save xxx|yarn upgrade xxx|更新某个依赖项目|
|npm install --global xxx|yarn global add xxx|全局安装某个依赖项目|
|npm config list|yarn config list|显示当前配置|
|npm list|yarn list|列出当前项目的所有依赖|
|npm run/test|yarn run/test|运行某个命令|
|npm xxx -h|yarn -h|查看某个命令帮助信息|
|npm help xxx|yarn xxx -h|查看命令文档|

