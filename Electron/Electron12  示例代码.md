## electron示例

main.js

```javascript
const {app, BrowserWindow, ipcMain, Menu, Tray, dialog} = require('electron');
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
    // backgroundColor: '#ccc', autoHideMenuBar: 'true',  frame: true, resizable: true, // 是否隐藏菜单栏, 无边框, 改变尺寸 
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
  // 引入菜单模块
  createMenu()
  // require('./mainProcess/menu')
  // 引入托盘图标模块
  crateTray(win);
}

// 创建about窗口
const showAboutWindow = () => {
  let win = new BrowserWindow({
    width: 250,
    height: 250,
    title: 'About',
    webPreferences: {
      nodeIntegration: true,
      contextIsolation: false
    }
  });
  win.webContents.openDevTools();
  win.loadFile(path.join(__dirname, './view/about.html'));
  // 设置当前窗口不显示菜单项
  win.setMenu(null);
}

function createMenu() {
  // 构建具体的菜单项
  // 菜单项选项参数参考文档：https://www.electronjs.org/docs/api/menu-item
  let template = [
    {
      // 定义菜单项名称
      label: '计算器',
      // 定义子项名称
      submenu: [
        {
          label: '关于',
          click: () => {
            showAboutWindow();
          }
        },
        {
          label: '退出',
          // 内置的 role 行为将提供默认预定义行为
          // role: 'quit',
          click: (item, win, event) => {
            // console.log(item, win, event);
            // 询问是否真的需要退出
            // 对话框文档: https://www.electronjs.org/docs/api/dialog
            dialog.showMessageBox({
              // 设置消息框的类型
              type: 'info',
              // 标题
              title: '退出提示',
              message: '请问是否真的需要退出',
              buttons: ['确定', '取消']
            }).then((result) => {
              const {response: index, checkboxChecked} = result;
              // index表示当前button的索引
              if (index == 0) {
                // 销毁窗体
                win.destroy();
              }
            })
          }
        }
      ]
    },
    {
      label: '格式',
      submenu: [
        {
          label: '颜色',
          // 定义键盘快捷键
          // 快捷键文档: https://www.electronjs.org/docs/api/accelerator
          accelerator: (() => {
            // 判断系统的类型
            if (process.platform === 'darwin') {
              // Mac
              return 'Command + Shift + C';
            } else {
              // Window
              return 'Control + Shift + C';
            }
          })(),
          click: (item, win, event) => {
            console.log('main-颜色')
          }
        },
        {
          label: '字体变大',
          accelerator: 'F11',
          click: (item, win, event) => {
            console.log('main-字体变大')
            // win 就是 BrowserWindow 对象
            // 向渲染进程发送事件
            win.webContents.send('add', 'big')
          }
        },
        {
          label: '默认字体',
          accelerator: 'F10',
          click: () => {
            //
          }
        }
      ]
    }
  ];
  // 为应用程序构建菜单项
  let menu = Menu.buildFromTemplate(template);
  // 将构建好的菜单项添加到应用
  Menu.setApplicationMenu(menu);
}

// 在主进程中监听渲染进程发送的显示右键菜单
ipcMain.on('showContext', (event) => {
  // BrowserWindow.fromWebContents可以获取当前窗口对象
  let win = BrowserWindow.fromWebContents(event.sender);
  // 将此菜单作为browserWindow中的上下文弹出
  menu.popup(win);
});

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
          const {response: index} = result;
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
index.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
<h1>Hello 你好</h1>
</body>
<script>
  require('./renderProcess/index')
</script>

</html>
```
renderProcess/index.js
```javascript
const {ipcRenderer} = require('electron');

// 右键菜单
document.oncontextmenu = () => {
    // 渲染进程向主进程发送信息
    ipcRenderer.send('showContext')
}

// 监听主进程发送的add事件
ipcRenderer.on('add', (event, msg) => {
    console.log('render-接收到主进程发送的事件add', msg);
});
```