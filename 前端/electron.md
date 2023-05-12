## Electron

### 基础

#### 主进程

##### BrowserWindow

​	创建并控制浏览器窗口。其实例针对的是窗口

- **closed**

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
    appId: "lethe.com",
    productName: "my-project", // 项目名，也是生成的安装文件名，即wyDemo.exe
    copyright: "lethe Copyright © 2022", // 版权信息
    files: ["./main.js", "./dist"]
    extraFiles: [
      // 把指定的资源复制到程序根目录，即把server文件夹的内容复制到程序根目录，这里server文件夹下的内容相当于我的后台，我在background.js中有相应的处理。
      "./server",
    ],
    directories: {
      output: "dists", // 输出文件路径
    },
    win: {
      // win相关配置
      // icon: "./favicon.ico", // 图标，当前图标在根目录下，注意这里有两个坑
      requestedExecutionLevel: "requireAdministrator", //获取管理员权限
      target: ["nsis", "zip"], // 利用nsis制作安装程序
    },
    nsis: {
      oneClick: false, // 是否一键安装
      allowElevation: true, // 允许请求提升。 如果为false，则用户必须使用提升的权限重新启动安装程序。
      allowToChangeInstallationDirectory: true, // 允许修改安装目录
      // installerIcon: "./favicon.ico", // 安装图标
      // uninstallerIcon: "./favicon.ico", // 卸载图标
      // installerHeaderIcon: "./favicon.ico", // 安装时头部图标
      createDesktopShortcut: true, // 创建桌面图标
      createStartMenuShortcut: true, // 创建开始菜单图标
      shortcutName: "leDom", // 图标名称(项目名称)
    },
}
```

vite.config.ts

```ts
export default defineConfig({
  plugins: [vue()],
  base: './' //改为相对路径
})
```
