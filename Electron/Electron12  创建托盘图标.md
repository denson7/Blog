
## 创建托盘图标
```
// 文档
https://www.electronjs.org/docs/api/tray
```
### 示例
```javascript
const { app, BrowserWindow, ipcMain, Menu, Tray, dialog } = require('electron');
const path = require('path');

let win = null;
app.on('ready', () => {
  createWindow()
});

function createWindow() {
  win = new BrowserWindow({
    // 宽度
    width: 800,
    // 高度
    height: 600,
    // 标题
    title: 'Demo',
    // 背景色
    // backgroundColor: '#ccc',
    webPreferences: {
      enableRemoteModule: true,
      // 设置渲染进程允许进程nodejs功能
      nodeIntegration: true,
      contextIsolation: false
    }
  });
  // 设置窗口加载的页面
  win.loadFile('./index.html');
  // 打开调试工具
  win.webContents.openDevTools()
  // 添加事件
  // 当窗口关闭的时候触发
  win.on('close', function (event) {
    // 将win重置为null
    // win = null;
    // 应用程序退出
    // app.quit();

    // 只是对窗口进行隐藏
    win.hide();
    // 隐藏任务栏图标, 使窗口不显示在任务栏中
    win.setSkipTaskbar(true);
    // 阻止默认行为
    event.preventDefault();
  });
  // 当窗口加载完毕之后，准备显示时触发
  app.on('ready-to-show', function (event) {
    console.log('ready-to-show')
    win.show();
    // 将当前窗口激活
    win.focus();
  });
  // 引入托盘图标模块
  crateTray(win);
}

// 创建系统托盘图标对象
// 文档：https://www.electronjs.org/docs/api/tray
let tray = null
function crateTray(win) {
  tray = new Tray(path.join(__dirname, './aa.ico'));

  // 创建系统托盘的上下文菜单--右键菜单
  const contextMenu = Menu.buildFromTemplate([
    {
      label: '关闭',
      click: () => {
        // 弹出提示对话框
        dialog.showMessageBox({
          // 设置消息框的类型
          type: 'info',
          // 标题
          title: '退出提示',
          message: '请问是否真的需要退出',
          buttons: ['确定', '取消']
        }).then((result) => {
          const { response: index } = result;
          // index表示当前button的索引
          if (index == 0) {
            // 销毁图标
            tray.destroy();
            // 销毁窗体
            win.destroy();
          }
        })
      }
    }
  ]);

  // 为系统托盘图标添加事件
  tray.on('click', () => {
    // 判断窗口是否可见
    if (win.isVisible()) {
      win.hide();
      win.setSkipTaskbar(true);
    } else {
      win.show();
      win.setSkipTaskbar(false);
    }
  })

  // 设置鼠标指针在托盘图标上悬停时显示的文本
  tray.setToolTip('Hello Electron');

  // 设置图标的内容(上下文)菜单 
  tray.setContextMenu(contextMenu)
}
```

