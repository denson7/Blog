[TOC]

### 打包组件库

```javascript
// 压缩插件
const TerserPlugin = require("terser-webpack-plugin")

module.exports = {
  mode:"none",
  entry: {
    'large-number': './src/index.js',
    'large-number-min': './src/index.js'
  },
  // 打包组件库
  output: {
    filename: '[name].js',
    library: 'largeNumber',
    libraryTarget: 'umd',
    libraryExport: 'default'
  },
  optimization:{
    minimize: true,
    minimizer: [
      new TerserPlugin({
        include: /\.min\.js$/
      })
    ]
  }
}
```

