## 运行单个实例
```
// 文档
https://www.electronjs.org/docs/api/app
```
当应用的主要实例app.requestSingleInstanceLock()返回true时，应该继续让程序运行。如果返回 false, 就立即退出。
```javascript
const gotTheLock = app.requestSingleInstanceLock()
if (!gotTheLock) {
  app.quit()
} {
  app.on('second-instance', (event, argv) => {
    if (process.platform === 'win32') {
      if (win) {
        if (win.isMinimized()) {
          win.restore()
        }
        if (win.isVisible()) {
          win.focus()
        } else {
          win.show()
        }
      }     }
  })
}
```
