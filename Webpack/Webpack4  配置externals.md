[TOC]
## 配置externals
```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');
const {resolve} = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'js/build.js',
    output: resolve(__dirname, 'build')
  },
  module: {
    rules: []
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html'
    })
  ],
  mode: 'production',
  // 配置externals
  externals: {
    // 忽略打包的文件, jQuery为npm包名
    // 备注: 这里忽略后,需要手动在index.html中引入jquery的cdn地址
    jquery: 'jQuery'
  }
};
```
上述方法缺点是：在webpack配置后，每次都需要在index.html模板中手动引入需要的cdn文件，有点麻烦了。
### 插件 html-webpack-externals-plugin
```javascript
// 动态添加CDN
const HtmlWebpackExternalsPlugin = require('html-webpack-externals-plugin');

module.exports = {
  plugins: [
    new HtmlWebpackExternalsPlugin({
      externals: [
        {
          // 引入的模块
          module: 'jquery',
          // cdn的地址
          entry: 'https://cdn.bootcss.com/jquery/3.4.1/jquery.min.js',
          // 挂载到了window上的名称
          // window.jQuery就可以全局使用
          global: 'jQuery'
        },
        {
          module: 'vue',
          entry: 'https://cdn.bootcss.com/vue/2.6.10/vue.min.js',
          global: 'Vue'
        }
      ]
    })
  ]
};
```