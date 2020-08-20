# electron底层进程间通信
https://www.iguan7u.cn/2019/06/30/Electron-%E8%BF%9B%E7%A8%8B%E9%97%B4%E9%80%9A%E8%AE%AF%E8%AF%A6%E8%A7%A3/
electron分为**主进程**和**渲染进程**
## 主进程
package.json中的main脚本称为主进程，该进程在应用整个生命周期只会存在唯一一个，负责界面窗口的创建、控制、销毁等管理行为，同时也能控制整个应用的生命周期    
主进程本质上是一个 Node.js 进程，该进程充分利用了 Node.js 的跨平台特性，在其 API 的底层，抹掉了不同操作系统中的差异
## 渲染进程
Electron 使用 Chromium 来展示 UI 界面，在应用程序中被称为 BrowserWindow当主进程每创建一个独立 BrowserWindow 实例，Electron 都会初始化一个独立的渲染进程，隔离了不同窗口之间的环境，使得每个窗口都只需要关心自己内部的 web 页面
## 通信方式
* localStorage,window.postMessage
* IPC:ipcRender、ipcMain，命名管道
