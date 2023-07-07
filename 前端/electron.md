## Electron

[electron官网]: https://www.electronjs.org/zh/docs/latest/

### 基础

#### 主进程

##### BrowserWindow

​	创建并控制浏览器窗口。其实例针对的是窗口

- **closed**

```js
let win = new BrowserWindow({
    x: 1920,  //窗口相对于主屏幕原点位置
    y: 0,
    fullscreen: true, //是否全面屏， width和height就可以忽略
    width: 800,
    height: 600,
    //show: false, //默认情况下创建一个窗口对象之后就会显示。
    title: "智慧驾驶",
    webPreferences: { //网页功能设置
        //nodeIntegration: true, //开启 node.js 环境集成
        //contextIsolation: false, //关闭上下文隔离
        //enableRemoteModule: true, //暴露remote模块

        preload: path.join(__dirname, 'preload.js'),
    }
})
```

##### app

​	控制应用程序的事件生命周期。主要针对的应用

- **ready** ： 当 Electron 完成初始化时，触发一次
- **window-all-closed** ：当所有的窗口都被关闭时触发。**你可以控制是否退出程序**
- **will-quit** ： 当所有窗口被关闭后触发，**同时应用程序将退出**。 调用 `event.preventDefault()` 将阻止终止应用程序的默认行为。
- **quit** ： 在应用程序退出时发出。

##### webContents

​	渲染以及控制web页面。`webContents`是一个[EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter). 负责渲染和控制网页, 是 [`BrowserWindow`](https://www.electronjs.org/zh/docs/latest/api/browser-window) 对象的一个属性。网页内的内容变化监听：

- **dom-ready** ： 当顶级 frame 的 document 被加载完时触发。

- **did-create-window** ：在渲染进程中由 `window.open` 成功创建窗口*之后* 触发。

#### 进程间的通信

##### ipcMain

```js
//监听 channel, 当新消息到达，将通过 listener(event, args...) 调用 listener
ipcMain.on(channel, listener)
//为一个 invokeable的IPC 添加一个handler。 每当一个渲染进程调用 ipcRenderer.invoke(channel, ...args) 时这个处理器就会被调用。
//返回值将被用来作为应答值
ipcMain.handle(channel, listener)
```

##### ipcRenderer

```js
//单向
ipcRenderer.send() 
//双向
ipcRenderer.invoke() 
```

##### 渲染进程到主进程（单向）

使用 [`ipcRenderer.send`](https://www.electronjs.org/zh/docs/latest/api/ipc-renderer) API 发送消息，然后使用 [`ipcMain.on`](https://www.electronjs.org/zh/docs/latest/api/ipc-main) API 接收。

#### 预加载

- 运行于渲染进程中，可以访问Node.js的部分API
- 与浏览器共享同一个全局 [`Window`](https://developer.mozilla.org/en-US/docs/Web/API/Window) 接口，使用 [`contextBridge`](https://www.electronjs.org/zh/docs/latest/api/context-bridge) 模块来安全地实现交互

**preload.js**

```js
//预加载文件，渲染进程之前执行
const { contextBridge, ipcRenderer } = require('electron')

//通过contextBridge暴露的api，渲染进程就可以通过window访问到
contextBridge.exposeInMainWorld('electronApi', {
  // 能暴露的不仅仅是函数，我们还可以暴露变量
  /* node: () => process.versions.node,
  chrome: () => process.versions.chrome,
  electron: () => process.versions.electron, */
  openWindow: (url) => { ipcRenderer.send('open-window', url) }
})
```

#### 父窗口与模态窗口

#### 自定义菜单

### 搭建桌面应用

[【electron】 vite + electron-builder 打包配置_electron+vite builder_姑老爷呀的博客-CSDN博客](https://blog.csdn.net/pfourfire/article/details/126974424)

技术栈： Vite + Ts + Vue3

#### 搭建vite+vue3+ts项目

```
yarn create my-electron --template vue-ts
```

#### 接入electron

安装electron相关包

```
yarn add electron -D
yarn add electron-builder -D
yarn add electron-devtools-installer
//concurrently开启多端口
yarn add concurrently
```

#### 启动electron

1. 创建主进程文件main.ts
2. 调整启动命令 package.json		

```json
"main": "src/main/main.ts", //设置入口文件，为main.ts文件所在的位置
"scripts": {
    "dev": "vite",
    //electron可以加载URL，在启动electron之前启动vue，然后将vue的访问入口接入electron，同时启动electron
    "electron:dev": " concurrently \"yarn dev\" \"electron .\" ", 
    "build": "vue-tsc && vite build",
    "preview": "vite preview"
}
```

#### 打包electron项目

​	先打包vue项目，然后利用electron-builder打包electron。

```json
 //package.json
{
    "electron:build": "yarn build && electron-builder",
    //electron-builder build --config electron-builder.json //也可以指定配置文件， 没有就在package.json的build字段
},
"build": {
    "appId": "ink.bennent_g.demo",
    "directories": {
        "output": "output"
    }
},
```

electron-builder.json

```json
{
    "appId": "remotecockpit.com",
    "productName": "智能驾驶舱", //项目名, 也是生成的安装文件名，即智能驾驶舱.exe
    "copyright": "zjzk Copyright © 2023",
    "files": [   //需要打包的文件
        "./electron", //mian.js preload.js文件
        "./dist", //前端打包的项目
        "./build/**/*"
    ],
    "extraFiles": [
        "./server"
    ],
    "directories": {
        "output": "dists"  //输出文件路径
    },
    "win": {
        "requestedExecutionLevel": "requireAdministrator",
        "target": [
            "nsis",
            "zip"
        ]
    },
    "mac":{},
    "linux": {},
    "nsis": {
        "oneClick": false, //是否一键安装
        "allowElevation": true, //允许请求提升。如果false，则用户必须使用提升的权限重新启动安装程序
        "allowToChangeInstallationDirectory": true, //允许修改安装目录
        "installerIcon": "./build/icons/aaa.ico", //安装图标
        "uninstallerIcon": "", //卸载图标
        "createDesktopShortcut": true, //创建桌面图标
        "createStartMenuShortcut": true, //创建开始菜单图标
        "shortcutName": "远程驾驶舱" //图标名称
    }
}
```

vite.config.ts

```ts
export default defineConfig({
  plugins: [vue()],
  base: './' //改为相对路径
})
```

vue.config.js

​	路由系统改为"hash"模式。

```js
{
    publicPath: "./"
}
```

