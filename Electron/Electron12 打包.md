## Electron 打包
### 使用 electron-builder 打包为安装包
```
// 安装
npm install -S electron-builder
```
修改package.json文件
```json
"script": {
  "build:win": "electron-builder --platform=win --arch=x64",
  "build:mac": "electron-builder --platform=mac --arch=x64"
},
// electron-builder 配置项
"build": {  // 这里是electron-builder的配置
  "productName": "xxxx",//项目名 这也是生成的exe文件的前缀名
  "appId": "com.xxx.xxxxx",//包名
  "copyright": "xxxx",//版权  信息
  "directories": { // 输出文件夹
    "output": "build"
  },
  // 需要打包的文件
  "files": [
    "dist/electron/**/*"
  ],
  // windows相关的配置
  "win": {
    "icon": "build/icons/aims.ico", //图标路径
    "target": [ // 构建目标
      {
        "target": "nsis", // 安装包
        "arch": [
          "ia32"
        ]
      }
    ]
  },
  // macOS
  "dmg":{
    "background":"res/background.png",//背景图片路径
    "window":{
      // 窗口左上角起始坐标
      "x":100,
      "y":100,
      // 窗口大小
      "width":500,
      "height":300
    }
  },
  // nsis配置指的是安装过程的配置, 不配置nsis则应用程序会默认安装在C盘
  "nsis": {
    "oneClick": false, // 是否一键安装
    "allowElevation": true, // 允许请求提升。 如果为false，则用户必须使用提升的权限重新启动安装程序。
    "allowToChangeInstallationDirectory": true, // 允许修改安装目录
    "installerIcon": "./build/icons/aaa.ico",// 安装图标
    "uninstallerIcon": "./build/icons/bbb.ico",//卸载图标
    "installerHeaderIcon": "./build/icons/aaa.ico", // 安装时头部图标
    "createDesktopShortcut": true, // 创建桌面图标
    "createStartMenuShortcut": true,// 创建开始菜单图标
    "shortcutName": "xxxx", // 图标名称
    "include": "build/script/installer.nsh", // 包含的自定义nsis脚本 这个对于构建需求严格得安装过程相当有用。
  },
}
```

### 使用 electron-packager 打包为可执行文件
```shell
// 安装
npm install -D electron-packager
```
修改package.json文件
```shell
// electron-packager <应用目录> <应用名称> <打包平台> --out=<输出目录> <架构> <应用版本> <忽略文件> <图标> --overwrite
electron-packager ./src demo --platform=win32 --out=./dist --arch=x64 --app-version=1.0.1 --icon=./images/icon.ico --overwrite
```
