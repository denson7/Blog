## 使用NodeJs
### 方法1 
在创建窗口时配置
```javascript
win= new BrowserWindow({
    width: 500,
    height: 400,
    frame: false,
    transparent: true,
    backgroundColor: '#00000000', // 当关闭开发者工具时, 会重新创建一个新的渲染视图, 所以会使用配置的背景颜色, 如果没配置会使用默认值白色
    webPreferences: {
      nodeIntegration: true, // 渲染层可以使用node
      webSecurity: false, // 跨域
      enableRemoteModule: true // 可以使用remote
    }
  })
```
### 方法2
如果是Vue，可以在vue.config.js配置
```javascript
module.exports = {
  pluginOptions: {
    electronBuilder: {
      nodeIntegration: true
    }
  }
}
```

