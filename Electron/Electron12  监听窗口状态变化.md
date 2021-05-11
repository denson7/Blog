

## 监听窗口的状态
```javascript
const win = new BrowserWindow({
width: 800,
height: 600,
webPreferences: {
  nodeIntegration: true
}
});
// 窗口最小化触发
win.on('minimize', () => {
  console.log('最小化')
})
win.on('focus', () => {
  console.log('聚焦')
})
// 窗口隐藏, 任务栏没有图标
win.on('hide', () => {
  console.log('隐藏')
})
win.on('show', () => {
  flashTray(false)
  console.log('显示')
})
```

## 设置默认最大化
```javascript
//主进程（main.js）
let win = new BrowserWindow({show: false})
win.maximize()
win.show()
```
## 设置全屏
```javascript
//主进程（main.js）
let win = new BrowserWindow({fullscreen: true})
```

