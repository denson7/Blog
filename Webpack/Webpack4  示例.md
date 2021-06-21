[TOC]

## 实例配置
```javascript
const path = require('path');
const os = require('os');
const webpack = require('webpack');
const CleanWebpackPlugin = require('clean-webpack-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const ProgressBarPlugin = require('progress-bar-webpack-plugin');
const HappyPack = require('happypack');
const LodashModuleReplacementPlugin = require('lodash-webpack-plugin');
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;
const happyThreadPool = HappyPack.ThreadPool({size: os.cpus().length});

const ASSET_PATH = process.env.ASSET_PATH || '/';//如果预先定义过环境变量，就将其赋值给`ASSET_PATH`变量，否则赋值为根目录
module.exports = {
  mode: "development",
  devtool: 'cheap-module-eval-source-map',
  entry: {
    main: './core/index.js'
  },
  output: {
    filename: '[name].[hash].js',
    path: path.resolve(__dirname, './dist'),
    publicPath: ASSET_PATH
  },
  // 生成环境
  // output: {
  //   // 每个文件都有自己的hash版本号
  //   filename: '[name].[chunkhash:8].js',
  //   chunkFilename: '[name].[chunkhash:8].js',
  //   sourceMapFilename: "sourcemaps/[file].map",
  //   //publicPath: '//xxx.com/open/xx/'
  // },
  resolve: {
    alias: {
      React: 'react',
      Redux: 'redux',
      ReactRedux: 'react-redux',
      ReactRouterDOM: 'react-router-dom',
      ReduxForm: 'redux-form',
      ReduxSaga: 'redux-saga',
      '@core': path.resolve(__dirname, './core'),
      '@system': path.resolve(__dirname, './system'),
      '@emr': path.resolve(__dirname, './emr'),
    }
  },
  module: {
    rules: [
      {
        enforce: "pre",
        test: /\.js$/,
        exclude: /node_modules/,
        include: [path.resolve(__dirname, './system/src'), path.resolve(__dirname, './core')],
        loader: "eslint-loader",
      },
      {
        test: /\.js$/,
        exclude: /(node_modules|bower_components)/,
        use: [
          {
            loader: 'happypack/loader?id=happyBabel',
            // loader: 'babel-loader?cacheDirectory=true',
            // options: {
            //   presets: [['@babel/preset-env', {modules: false}]]
            // }
          }
        ]
      },
      {
        test: /\.css$/,
        //exclude: /(node_modules|bower_components)/,//某些CSS在node_modules
        use: ['style-loader', 'css-loader']
      },
      {
        test: /\.(png|jpg|gif|svg)$/,
        use: [
          {
            loader: 'file-loader',
            options: {}
          }
        ]
      },
      {
        test: /\.(woff|woff2|eot|ttf|otf)$/,
        use: [
          'file-loader'
        ]
      },
      {
        test: /\.(csv|tsv)$/,
        use: [
          'csv-loader'
        ]
      },
      {
        test: /\.xml$/,
        use: [
          'xml-loader'
        ]
      }
    ]
  },
  plugins: [
    new CleanWebpackPlugin(['dist']),
    new HtmlWebpackPlugin({
      title: '大数据运营系统',
      filename: "./index.html",
      inject: true,
      template: path.resolve(__dirname, './index.html'),
      minify: {
        removeComments: true,
        collapseWhitespace: true,
        removeRedundantAttributes: true,
        useShortDoctype: true,
        removeEmptyAttributes: true,
        removeStyleLinkTypeAttributes: true,
        keepClosingSlash: true,
        minifyJS: true,
        minifyCSS: true,
        minifyURLs: true
      }
    }),
    /*new webpack.ProvidePlugin({
      _: 'lodash'
    }),*/
    // 定义环境变量
    new webpack.DefinePlugin({
      'process.env.language': "zh",
      'process.env.ASSET_PATH': JSON.stringify(ASSET_PATH),
    }),
    // 为编译添加进度条
    new ProgressBarPlugin(),
    // 压缩lodash
    new LodashModuleReplacementPlugin(),
    // 生成编译结果分析报告
    new BundleAnalyzerPlugin(),
    // 单线程打包
    new HappyPack({
      //用id来标识 happypack处理那里类文件
      id: 'happyBabel',
      //如何处理  用法和loader 的配置一样
      loaders: [{
        loader: 'babel-loader',
        // here you configure babel:
        options: {
          babelrc: true,
          cacheDirectory: true,
          plugins: ['lodash'],
          presets: [['@babel/preset-env', {modules: false}]
          ]}
      }],
      //共享进程池
      threadPool: happyThreadPool,
      //允许 HappyPack 输出日志
      verbose: true,
    })
  ],
  optimization: {
    splitChunks: {
      cacheGroups: {
        vendor: {
          // test: /[\\/]node_modules[\\/]/,
          // 长效缓存那些稳定的基本不变的库，例如 react前缀开头 的库
          // 依赖不稳定的库不放vendor里，比如@tencent库因入口模块在需引用，不确定会引入多少模块
          test: /[\\/]node_modules[\\/](react|redux|moment|core-js|lodash)/,
          name: 'vendor',
          chunks: 'initial',
          priority: -2
        },
        // echarts 独立的第三方库，体积很大，单独打包，由各级页面依赖
        echarts: {
          test: /[\\/]node_modules[\\/](echarts|zrender)[\\/]/,
          name: 'echarts',
          chunks: 'all',
          priority: 2
        }
      }
    },
    // Keep the runtime chunk seperated to enable long term caching
    runtimeChunk: true
  },
  devServer: {
    contentBase: './dist',//path.join(__dirname, "dist"),
    disableHostCheck: true,
    host: "g.cloud.tencent.com",
    port: 80,
    historyApiFallback: true
  }
};
```

## 安装说明
使用webpack，需要安装
```
npm install -D webpack webpack-dev-server webpack-cli html-webpack-plugin extract-text-webpack-plugin
```
使用react，需要安装
```
npm install -S react react-dom react-router-dom
```
处理less，需要安装
```
npm install -D css-loader style-loader less less-loader
```
处理图片和字体文件，需要安装
```
npm install -D url-loader file-loader
```
支持高级语法-babel，需要安装
```
npm install -S babel-polyfill // 实际生产环境也会用到
npm install -D babel-core babel-loader babel-preset-env babel-preset-react babel-preset-stage-2 
```
配置eslint，需要安装
```
npm install -D eslint eslint-loader eslint-plugin-react babel-eslint
```
使用mock模拟数据，需要安装
```
//koa： 一个简单好用的 Web 框架
//koa-router：常用的 koa 的路由库
//koa-bodyparser：用来解析body的中间件
//mockjs：生成随机数据，模拟数据
npm install -D koa koa-router koa-bodyparser mockjs
```
实时监测koa服务，需要安装
```
npm install -D nodemon
```
使用fetch发起请求这里用whatwg-fetch包，需要安装
```
npm install whatwg-fetch -S
```
一次启动两个服务，需要安装
```
npm install concurrently -D
```

### 配置.eslintrc.js
在根目录新建.eslintrc.js文件，添加如下内容

```javascript
module.exports = {
  "env": {
    "browser": true,
    "commonjs": true,
    "es6": true
  },
  "extends":[
    "eslint:recommended",
    'plugin:react/recommended',
  ],
  "parser": 'babel-eslint',
  "parserOptions": {
    "ecmaFeatures": {
      "experimentalObjectRestSpread": true,
      "jsx": true
    },
    "sourceType": "module"
  },
  "plugins": [
    "react"
  ],
  "rules": {
    "indent": [
      "error",
      4
    ],
    "linebreak-style": [
      "error",
      "windows"
    ],
    "quotes": [
      "error",
      "single"
    ],
    "semi": [
      "error",
      "never"
    ],
    'no-debugger': process.env.NODE_ENV === 'production' ? 2 : 0,
    'no-console': 0
  }
};
```
### 配置.babelrc
在根目录新建.babelrc文件，添加如下内容：
```javascript
{
  "presets": [
    ["env",{
      "targets": {
        "browsers": ["IE >= 9"]
      },
      "useBuiltIns": true
    }],
    "react",
    "stage-2"
  ],
  "plugins": [

  ]
}
```

### package.json
```javascript
{
  "name": "react示例",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "mock": "nodemon ./mock/mock-server.js",
    "dev": "concurrently \"npm run mock\" \"npm start\" ",
    "build": "webpack --config webpack.pro.config.js",
    "start": "webpack-dev-server --mode development"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "babel-eslint": "^8.2.3",
    "concurrently": "^3.6.0",
    "eslint": "^4.19.1",
    "eslint-loader": "^2.0.0",
    "eslint-plugin-react": "^7.9.1",
    "extract-text-webpack-plugin": "^3.0.2",
    "html-webpack-plugin": "^3.2.0",
    "koa": "^2.5.1",
    "koa-bodyparser": "^4.2.1",
    "koa-router": "^7.4.0",
    "mockjs": "^1.0.1-beta3",
    "nodemon": "^1.17.5",
    "webpack": "^4.12.0",
    "webpack-cli": "^3.0.8",
    "webpack-dev-server": "^3.1.4"
  },
  "dependencies": {
    "babel-core": "^6.26.3",
    "babel-loader": "^7.1.4",
    "babel-polyfill": "^6.26.0",
    "babel-preset-env": "^1.7.0",
    "babel-preset-react": "^6.24.1",
    "babel-preset-stage-2": "^6.24.1",
    "css-loader": "^0.28.11",
    "file-loader": "^1.1.11",
    "less": "^3.0.4",
    "less-loader": "^4.1.0",
    "react": "^16.4.1",
    "react-dom": "^16.4.1",
    "react-router-dom": "^4.3.1",
    "style-loader": "^0.21.0",
    "url-loader": "^1.0.1",
    "whatwg-fetch": "^2.0.4"
  }
}
```
## webpack.config.js
```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const webpack = require('webpack');

module.exports = {
  mode:'development',
  entry:{
    main: './src/index.js'
  },
  output:{
    path:path.resolve(__dirname,'dist'),
    filename:'js/bundle.js'
  },
  module:{
    rules:[
      {
        test:/\.(js|jsx)$/,
        use: "babel-loader",// 使用eslint检测["babel-loader", "eslint-loader"]
        exclude:/node_modules/
      },
      // 处理css
      {
        test: /\.(css|less)$/,
        use: [
          'style-loader',
          {
            loader: 'css-loader',
            options: { importLoaders: 1 }
          },
          'less-loader'
        ]
      },
      {
        test: /\.(woff2?|eot|ttf|otf)(\?.*)?$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: '1024'
            }
          },
        ]
      },
      {
        test: /\.(woff|eot|ttf|svg|png|jpg|gif)$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: '1024',
            }
          },
        ]
      },
      // 处理图片资源
      // {
      //   test: /\.(svg|png|jpg|gif)/,
      //   loader: 'url-loader',
      //   options: {
      //     limit: 8 * 1024,
      //     name: '[hash:10].[ext]',
      //     // 关闭es6模块化(在使用html-loader时关闭)
      //     esModule: false,
      //     outputPath: 'imgs' // 指定打包后的输出路径
      //   }
      // },
      // // 处理html中img资源
      // {
      //   test: /\.html$/,
      //   loader: 'html-loader'
      // },
      // // 处理其他资源
      // {
      //   exclude: /\.(html|js|css|less|jpg|png|gif)/,
      //   loader: 'file-loader',
      //   options: {
      //     name: '[hash:10].[ext]',
      //     outputPath: 'media'
      //   },
      // },
      //进行语法检测,这段可省略
      {
        enforce: 'pre',
        test: /.(js|jsx)$/,
        loader: 'eslint-loader',
        exclude: [
          path.resolve(__dirname, '../node_modules')
        ]
      }
    ]
  },
  plugins:[
    //将webpack打包后的文件插入到index.html中
    new HtmlWebpackPlugin({template:'./src/index.html'}),
  ],
  devServer:{
    contentBase: path.join(__dirname, "dist"),
    compress: true,
    open: true,
    port: 9000,
    proxy: {
      "/api": {
        target: "http://127.0.0.1:3000/",
        pathRewrite: {"^/api" : ""}
      }
    }
  }
};
```
## webpack.pro.config.js
```javascript
const path = require('path')
const webpack = require('webpack')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const ExtractTextPlugin = require('extract-text-webpack-plugin')

module.exports = {
  entry:{
    main:['babel-polyfill','./src/index.js'],
    vendors: ['react','react-dom','react-router-dom','whatwg-fetch']
  }
  ,
  output:{
    path:path.resolve(__dirname,'dist'),
    filename:'bundle.[hash:4].js'
  },
  module:{
    rules:[
      {
        test: /\.(woff|eot|ttf|svg|png|jpg)$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: '1024' ,
              name: '[name].[hash:4].[ext]'
            }
          },
        ]
      },
      {
        test: /\.(woff2?|eot|ttf|otf)(\?.*)?$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: '1024',
              name: '[name].[hash:4].[ext]'
            }
          },
        ]
      },
      {
        test: /\.(css|less)$/,
        use: ExtractTextPlugin.extract({
          fallback: 'style-loader',
          use: ["css-loader","less-loader"]
        })
      },
      {
        test:/\.(js|jsx)$/,
        use:"babel-loader",
        exclude:/node_modules/
      }
    ]
  },
  // devtool:"source-map",//源码映射，会单独生成一个sourcemap文件(大而全)
  // devtool:"eval-source-map",//不会生成单独的文件，代码出错调试可以显示行和列
  devtool:"cheap-module-eval-source-map",//(推荐)不会生成文件，控制台可以源码进行调试
  plugins:[
    // html 模板插件
    new HtmlWebpackPlugin({template:'./src/index.html',favicon: './public/favicon.png'}),
    // 启用作用域提升,让代码文件更小、运行的更快
    new webpack.optimize.ModuleConcatenationPlugin(),
    // 提取公共代码vendors
    new webpack.optimize.CommonsChunkPlugin({
      name:'vendors',
      filename:'[name].[hash:4].js'
    }),
    // 抽离出css
    new ExtractTextPlugin("style.css"),
    // 压缩js代码
    new webpack.optimize.UglifyJsPlugin({
      compress: {
        warnings: false,
        drop_console: true,
        pure_funcs: ['console.log']
      }
    }),
    // 定义全局常量
    new webpack.DefinePlugin({
      "process.env": {
        "NODE_ENV": JSON.stringify("production")
      }
    }),
    // 加署名
    new webpack.BannerPlugin('Copyright by Zero https://github.com/l-Lemon/blog')
  ]
}
```
## 抽离公共配置
```javascript
// #webpack.base.js
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const ExtractTextPlugin = require('extract-text-webpack-plugin')
// 抽离css
const extractCss = new ExtractTextPlugin("style.css")
// html 模版
const htmlTemplate = new HtmlWebpackPlugin({
  template:'./src/index.html',
  favicon: './public/favicon.png'
})

const config = {
  output:{
    path:path.resolve(__dirname,'dist'),
    filename:'bundle.[hash:4].js'
  },
  module:{
    rules:[
      {
        test: /\.(woff|eot|ttf|svg|png|jpg)$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: '1024' ,
              name: '[name].[hash:4].[ext]'
            }
          },
        ]
      },
      {
        test: /\.(woff2?|eot|ttf|otf)(\?.*)?$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: '1024',
              name: '[name].[hash:4].[ext]'
            }
          },
        ]
      },
      {
        test: /\.(css|less)$/,
        use: ExtractTextPlugin.extract({
          fallback: 'style-loader',
          use: ["css-loader","less-loader"]
        })
      },
      {
        test:/\.(js|jsx)$/,
        use:"babel-loader",// use:["babel-loader", "eslint-loader"],
        exclude:/node_modules/
      }
    ]
  },
}

module.exports = {
  htmlTemplate,
  extractCss,
  config
}

// #webpack.config.js
//公共配置提取到webpack.base.js中
const path = require('path')
const baseConfig = require('./webpack.base')

module.exports = {
  entry:{
    main:['babel-polyfill','./src/index.js'],
  },
  ...baseConfig.config,
  plugins:[
    baseConfig.htmlTemplate,
    baseConfig.extractCss
  ],
  devServer:{
    contentBase: path.join(__dirname, "dist"),
    compress: true,
    port: 9000,
    proxy: {
      "/api": {
        target: "http://127.0.0.1:3000/",
        pathRewrite: {"^/api" : ""}
      }
    }
  }
}

// #webpack.pro.config.js
const webpack = require('webpack')
const baseConfig = require('./webpack.base')
//公共配置提取到webpack.base.js中
module.exports = {
  entry:{
    main:['babel-polyfill','./src/index.js'],
    // 将第三方库包单独打包，充分利用浏览器缓存
    vendors: ['react','react-dom','react-router-dom','whatwg-fetch']
  },
  ...baseConfig.config,
  devtool:false,
  plugins:[
    // html 模板插件
    baseConfig.htmlTemplate,
    // 启用作用域提升,让代码文件更小、运行的更快
    new webpack.optimize.ModuleConcatenationPlugin(),
    // 提取公共代码vendors
    new webpack.optimize.CommonsChunkPlugin({
      name:'vendors',
      filename:'[name].[hash:4].js'
    }),
    // 抽离出css
    baseConfig.extractCss,
    // 压缩js代码
    new webpack.optimize.UglifyJsPlugin({
      compress: {
        warnings: false,
        drop_console: true,
        pure_funcs: ['console.log']
      }
    }),
    // 定义全局常量
    new webpack.DefinePlugin({
      "process.env": {
        "NODE_ENV": JSON.stringify("production")
      }
    }),
    // 加署名
    new webpack.BannerPlugin('Copyright by xx https://github.com/lxx')
  ]
}
```

