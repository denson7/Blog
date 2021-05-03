[TOC]
## Vue3 使用组件库Element-Plus
### 安装 Element Plus
```powershell
$ npm install --save element-plus
+ element-plus@1.0.2-beta.41
```
### 全局引入
```js
import { createApp } from 'vue';
import App from './App.vue';
import router from './router';
import store from './store';
// 全局引入
import ElementPlus from 'element-plus';

import 'element-plus/lib/theme-chalk/index.css';

const app = createApp(App);
// 注册全部组件
app
  .use(ElementPlus)
  .use(store)
  .use(router)
  .mount('#app');
```
### 动态按需引入
```js
import { createApp } from 'vue';
import App from './App.vue';
import router from './router';
import store from './store';
// 动态引入单个组件，按需引入
import { ElButton } from 'element-plus';
// 手动引入组件样式
import 'element-plus/lib/theme-chalk/el-button.css';
const app = createApp(App);
// 注册单个组件
app.component(ElButton.name, ElButton);
app
  .use(store)
  .use(router)
  .mount('#app');
```
采用此方法动态引入，可以保证是按需引入，但缺点是每引入一个组件，都要手动将其对应的样式文件同时引入，过于麻烦，解决方法是：使用`babel-plugin-import`插件辅助完成这个工作。
```js
// 安装 babel-plugin-import 插件,加上 --save-dev 选项,表示开发环境依赖
$ npm install --save-dev babel-plugin-import
// 修改配置文件babel.config.js
module.exports = {
  presets: ['@vue/cli-plugin-babel/preset'],
  // 新增代码
  plugins: [
    [
      'import',
      {
        libraryName: 'element-plus',
        customStyleName: (name) => {
          return `element-plus/lib/theme-chalk/${name}.css`;
        },
      },
    ],
  ],
};
// 再修改main.js入口文件
import { createApp } from 'vue';
import App from './App.vue';
import router from './router';
import store from './store';
// 动态引入单个组件，按需引入
import { ElButton } from 'element-plus';
// 手动引入组件样式, 使用 babel-plugin-import 插件后，无需手动引入样式文件
// import 'element-plus/lib/theme-chalk/el-button.css';
const app = createApp(App);
// 注册单个组件
app.component(ElButton.name, ElButton);
app
  .use(store)
  .use(router)
  .mount('#app');
// 修改模板文件
<template>
  <el-button type="primary">Primary</el-button>
</template>
```




