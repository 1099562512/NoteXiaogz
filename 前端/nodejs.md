## NodeJS

[Node中文网]: https://www.nodeapp.cn/

​	在浏览器外运行 V8 JavaScript 引擎（Google Chrome 的内核），利用**事件驱动、非阻塞和异步输入输出模型**等技术提高性能。可以理解为 `Node.js` 就是一个服务器端的、非阻塞式I/O的、事件驱动的`JavaScript`运行环境

> **特点**

- 基于事件驱动
- 异步式I/O（非阻塞I/O）
- 单进程、单线程
- 依赖于Chrome V8引擎进行代码解释
- 轻量、可伸缩、适用于实时的数据交互应用

内置http服务、path、process、fs

> **核心组成**

- 第一层： Natives modules： 暴露JS Api供开发者使用

- 第二层： Node C/C++ Bindings： 通过C/C++调用底层功能，需要v8配合
- 第三层
  -  v8: 执行js代码，提供桥梁接口
  - libuv: 事件循环，事件队列，异步I/O
  - 第三方模块: zlib、http、c-ares等

### 事件驱动

​	 事件驱动就是当进来一个新的请求的时，请求将会被压入一个事件队列中，然后通过一个循环来检测队列中的事件状态变化，如果检测到有状态变化的事件，那么就执行该事件对应的处理代码，一般都是回调函数 

 ![img](https://camo.githubusercontent.com/da0ce70ad5bea183b672dbdecab2e3f77e18385dca79ca8f005fa6304b1ad69c/68747470733a2f2f7374617469632e7675652d6a732e636f6d2f61373732393539302d633165382d313165622d616239302d6439616538313462323430642e706e67) 

​	优缺点如下：

- 优点
  - 处理高并发场景性能更佳
  - 适合I/O密集型应用，值的是应用在运行极限时，CPU占用率仍然比较低，大部分时间是在做 I/O硬盘内存读写操作
- 缺点
  - 不适合CPU密集型应用
  - 只支持单核CPU，不能充分利用CPU
  - 可靠性低，一旦代码某个环节崩溃，整个系统都崩溃

### 异步I/O

​	每个平台都提供了不同异步I/O的调用方法如epoll、kqueue、event ports，libuv就是通过调用这些。

#### 非阻塞异步

 	IO调用才是应用程序干的事情，而IO执行是操作系统的工作。在IO调用时，对待操作系统IO就绪状态的不同方式，决定了其是阻塞或非阻塞模式；在IO执行时，线程或进程是否挂起等待IO执行决定了其是否为同步或异步IO。 

​	`Nodejs`采用了非阻塞型`I/O`机制，在做`I/O`操作的时候不会造成任何的阻塞，当完成之后，以时间的形式通知执行操作

​	例如在执行了访问数据库的代码之后，将立即转而执行其后面的代码，把数据库返回结果的处理代码放在回调函数中，从而提高了程序的执行效率

### 单线程

​		Node的主线程是单线程，livbuv里面是有线程池的。异步都是并发执行，

### 应用场景

借助`Nodejs`的特点和弊端，其应用场景分类如下：

- 善于`I/O`，不善于计算。因为Nodejs是一个单线程，如果计算（同步）太多，则会阻塞这个线程
- 大量并发的I/O，应用程序内部并不需要进行非常复杂的处理
- 与 websocket 配合，开发长连接的实时交互应用程序

具体场景可以表现为如下：

- 第一大类：用户表单收集系统、后台管理系统、实时交互系统、考试系统、联网软件、高并发量的web应用程序
- 第二大类：基于web、canvas等多人联网游戏
- 第三大类：基于web的多人实时聊天客户端、聊天室、图文直播
- 第四大类：单页面浏览器应用程序
- 第五大类：操作数据库、为前端和移动端提供基于`json`的API

其实，`Nodejs`能实现几乎一切的应用，只考虑适不适合使用它

### HTTP模块

### Process

​	 `process` 对象是一个全局变量，提供了有关当前 `Node.js `进程的信息并对其进行控制，作为一个全局变量 

​	 当我们启动一个`js`文件，实际就是开启了一个服务进程，每个进程都拥有自己的独立空间地址、数据栈，像另一个进程无法访问当前进程的变量、数据结构，只有数据通信后，进程之间才可以数据共享 

### fs模块

#### readdirSync

```
fs.readdirSync(path[, options])
```

 同步的 [readdir(3)](http://man7.org/linux/man-pages/man3/readdir.3.html). 返回一个不包括 `'.'` 和 `'..'` 的文件名的数组。 

#### 批量引入js文件

```js
const express = require('express');
const { join } = require('path');
const fs = require('fs')
const app = express();

const routers = []
//readdirSync读取目录, 返回一个文件数组
const files = fs.readdirSync('./routes') //["index.js", "user.js"]
files.forEach(file => {
  const path = join(__dirname, './routes', file)
  let stat = fs.statSync(path)
  routers.push(require(path))
})

routers.forEach( route => {
  app.use(route)
})
```

### Buffer

- 内置模块
- 进行Node平台下的二进制数据操作
- 不占据V8堆内存大小的内存空间
- 内存的使用由Node控制，由V8的GC回收
- 一般配合Stream流使用，充当数据缓冲区