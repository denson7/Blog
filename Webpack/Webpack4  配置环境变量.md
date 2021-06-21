[TOC]
## 使用DefinePlugin 配置环境变量
```javascript
// package.json
"scripts": {
  "dev": "cross-env NODE_ENV=development webpack-dev-server --mode=development --config config/webpack.dev.js",
  "build:dev": "cross-env NODE_ENV=development API=dev webpack --mode=development --config config/webpack.prod.js",
  "build:pro": "cross-env NODE_ENV=production API=pro webpack --mode=production --config config/webpack.prod.js",
  "build:test": "cross-env NODE_ENV=production API=test webpack --config config/webpack.prod.js",
  "fix": "eslint --fix --ext .js --ext .jsx  src/",
  "lint": "eslint --ext .jsx --ext .js src"
},
```
```javascript
// webpack配置全局变量
plugins: [
  new webpack.DefinePlugin({
    "process.env.NODE_ENV": JSON.stringify(process.env.NODE_ENV), 
    "process.env.API": JSON.stringify(process.env.API)
  })
],
```
```javascript
// 使用
if (process.env.API=== "dev") {
  axios.defaults.baseURL = "";
} else if (process.env.API=== "pro") {
  axios.defaults.baseURL = "";
} else if (process.env.API=== "test") {
  axios.defaults.baseURL = "";
} else {
  axios.defaults.baseURL = "";
}
```

