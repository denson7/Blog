
## 生命周期
```
//文档
https://www.electronjs.org/docs
#
app 用于控制应用生命周期。
1.ready：当Electron完成初始化时触发。
2.dom-ready：一个框架中的文本加载完成后触发该事件。
3.did-finish-load：导航完成时触发，即选项卡的旋转器将停止旋转，并指派onload事件后。
4.window-all-closed：当所有窗口被关闭时触发。
5.before-quit：在应用程序关闭窗口之前触发。
6.will-quit：当所有窗口已关闭并且应用程序都已经退出时触发。
7.quit：当所有窗口被关闭时触发。
8.closed：当窗口关闭时触发，收到此事件后，应删除对窗口的引用，并避免再使用它。

// 在VS Code终端执行$ chcp 65001 解决中文乱码
```
## 主进程 Main Process
Electron运行package.json的main脚本的进程被称之为主进程。每个应用只有一个主进程，它可以管理原生GUI，创建渲染进程，控制应用生命周期(app)。
```
特点：
可以使用和系统对接的ElectronAPI 创建菜单，上传文件等
创建渲染进程 Renderer Process
全面支持Node.js
只有一个，作为整个程序的入口
```
## 渲染进程 Renderer Process
展示Web页面的进程称之为渲染进程，它可以通过Node.js，Electron提供的API与系统底层打交道，一个Electron应用可以有多个渲染进程。
```
特点：
可以有多个，每一个对应一个窗口
每一个都是单独的进程
全面支持Node.js和Dom API
可以使用部分Electron 提供的API
```

