## Electron 进程间的通信
### 进程间通信的目的
1.通知事件
例如在页面中想去创建一个原生菜单，但只有主进程才能创建原生菜单，所以只有通过IPC进行进程间的通信，去让主进程创建我们需要的菜单。
２.数据传输
例如想在某个页面获取现在的内存情况，就需要将这些数据通过IPC来传输。
３.共享数据
例如像用户信息，在各个进程都会用到，所以就需要通过IPC通信来完成数据的共享。

### IPC模块通信
Electron提供了IPC通信模块，主进程的ipcMain和渲染进程的ipcRenderer
ipcMain和ipcRenderer都是EventEmitter对象。

### 从渲染进程到主进程
#### Callback写法
ipcRenderer.send(channel, ...args)
ipcMain.on(channel, handler)
```javascript
// 主进程
const { app, BrowserWindow, ipcMain } = require('electron');
const path = require('path');

let win = null;
app.on('ready', () => {
  win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: path.join(__dirname, 'renderer.js'),
      nodeIntegration: true, // 渲染层可以使用node
    },
  });
  win.webContents.openDevTools();
  win.loadFile('index.html');
  handleIPC();
  win.on('closed', function () {
    win = null;
  });
});

function handleIPC() {
  // 监听渲染进程向主进程发送事件
  ipcMain.on('do-work', (event, msg1, msg2) => {
    // 打印渲染进程传递的参数
    console.log(msg1, msg2);
  });
}
// 渲染进程
const { ipcRenderer } = require('electron');

ipcRenderer.send('do-work', '渲染进程参数1', '渲染进程参数2');
```
#### Promise写法
ipcRenderer.invoke(channel, ...args)
ipcMain.handle(channel, handler)
```javascript
// 主进程
const { app, BrowserWindow, ipcMain } = require('electron');
const path = require('path');

let win = null;
app.on('ready', () => {
  win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: path.join(__dirname, 'renderer.js'),
      nodeIntegration: true,
    },
  });
  win.webContents.openDevTools();
  win.loadFile('./index.html');
  handleIPC();
  win.on('closed', function () {
    win = null;
  });
});

function handleIPC() {
  // 处理渲染进程的channel请求
  ipcMain.handle('do-work', async function () {
    let res = await new Promise((resolve, reject) => {
      resolve('confirm');
      // resolve('cancel');
    });
    return res;
  });
}
// 渲染进程
const { ipcRenderer } = require('electron');

async function testRenderer() {
  // 渲染进程向主进程发送请求
  let res = await ipcRenderer.invoke('do-work');
  if (res === 'confirm') {
    setTimeout(() => {
      console.log('confirm');
    }, 2000);
  } else if (res === 'cancel') {
    console.log('cancel');
  }
}
testRenderer();
```

### 从主染进程到渲染进程
ipcRenderer.on(channel, handler)
webContents.send(channel)
示例
```javascript
// 渲染进程
const {ipcRenderer} = require('electron')
ipcRenderer.send('message', 'ping') // 发送给主进程
ipcRenderer.on('message-reply', (event,msg) => {
  console.log(msg); // pong 消息来自主进程
}

// 主进程
const {BrowserWindow, ipcMain} = require('electron')
// 监听渲染进程信息
ipcMain.on('message', (event,msg) => {
  console.log(msg); // 消息来自渲染进程
  // 方法1 通过event.sender.send() 发送信息到渲染进程
  // 备注：此方法只能点对点接收, 渲染进程A发信息到主进程，主进程只能发给渲染进程A，不能通过此方法再将数据发送给渲染进程B
  event.sender.send('message-reply', 'pong') // 回复子程序
})
win = new BrowserWindow({...})
// 如果触发did-finish-load事件，说明webContents加载完毕
win.webContents.on('did-finish-load', () => {
    // 方法2：此时再使用webContents.send()方法, 实现主进程单独往渲染进程发信息
    win.webContents.send('message-reply', 'pong')
  })
```

### 页面间(渲染进程与渲染进程间)通信
#### 使用全局共享属性
```javascript
// 在主进程中，设置一个全局对象，用来存储不同渲染进程间需要共享的数据，挂在全局对象global下
global.sharedObject = {
  user: ''
}

//渲染进程一
const {remote} = require('electron')
remote.getGlobal('sharedObject').user = 'John'

//渲染进程二
const {remote} = require('electron')
console.log(remote.getGlobal('sharedObject').user) //xmanlin
```
#### 通知事件
通过主进程转发
```javascript
//主进程
ipcMain.on('msg1', (event, message) => {
  yourWindow.webContents.send('msg2', message);
}
//渲染进程1
ipcRenderer.send('msg1', '来自渲染进程1的消息')
//渲染进程2
ipcRenderer.on('msg2', (event, message) => {
    console.log(message)  //来自渲染进程1的消息
  }
)
```
ipcRenderer.sendTo
```javascript
// 主进程
//创建一个新的渲染进程
let win2 =  new BrowserWindow({
    width: 800,
    height: 600,
})

//为渲染进程设置唯一id
win2.id = 2

// 渲染进程1
const {ipcRenderer} = require('electron')
//向id为2的渲染进程发送消息
ipcRenderer.sendTo(2,'msg1','来自渲染进程1的消息')

// 渲染进程2
const {ipcRenderer} = require('electron') 
ipcRenderer.on('msg1', (event, message) => {
console.log(message); // 来自渲染进程1的消息 
})
```
#### 数据共享
Web技术(localStorage，sessionStorage，indexedDB)

