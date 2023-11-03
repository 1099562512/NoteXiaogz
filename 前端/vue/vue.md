## MVVM

### 核心特性

- 数据驱动（MVVM）
- 组件化  模板编译
- 指令系统

### 双向绑定 MVVM

ModelView的主要职责：

- 数据变化后更新视图

- 视图变化后更新数据

当然，它还有两个主要部分组成

- 监听器（Observer）：对所有数据的属性进行监听
- 解析器（Compiler）：对每个元素节点的指令进行扫描跟解析,根据指令模板替换数据,以及绑定相应的更新函数

vue双向绑定流程

1. new vue()首先初始化，对data执行响应化处理，发生在observe中
2. 同时对模板执行编译，找到其中动态绑定的数据，从data中获取并初始化视图，这个过程发生在complier
3. 同时定义一个Watcher，将来对应数据变化时，Watcher会调用更新函数
4. 由于data的某个key在一个视图中可能出现多次，所以**每个key都需要一个管家Dep来管理多个watcher**
5. **将来data的数据一旦发生变化，会首先找到对应的Dep，通知所有的Watcher调用更新函数**

 ![img](https://camo.githubusercontent.com/c5cf751d6ce2c551e3a6b63279215c50c3348093263084f28b1132bdb509a342/68747470733a2f2f7374617469632e7675652d6a732e636f6d2f65353336393835302d336163392d313165622d383566362d3666616337376330633962332e706e67) 

## vue3

vue2.x使用的是es5的object.defineProperty， vue3用的是es6的proxy。

object.defineProperty的缺点：

- 无法监听对象属性的删除和添加
- 不能监听数组的变化，除了push/pop/unshift/shift/splice/reverse
- 只能遍历对象属性直接修改(需要深拷贝进行修改)

Proxy

- 直接监听对象而非属性
- 直接监听数组的变化
- 拦截的方式有很多种(13种，get、has、set)
- Proxy返回一个新对象

### composition API和options API

​	通常使用`Vue2`开发的项目，普遍会存在以下问题：

- 代码的可读性随着组件变大而变差
- 每一种代码复用的方式，都存在缺点
- TypeScript支持有限

​    在 Vue3 Composition API 中，组件根据逻辑功能来组织的，一个功能所定义的所有 API 会放在一起（更加的高内聚，低耦合）即使项目很大，功能很多，我们都能快速的定位到这个功能所用到的所有 API

### 性能优化

- 更小

  删除一些不常用的api， 引入`tree-shaking`，可以将无用模块“剪辑”，仅打包需要的，使打包的整体体积变小了 

- 更快

- 性能提升

  - diff算法优化： vue3在算法中加入了静态标记， 对不参与更新的元素，会做静态提升，只会被创建一次，在渲染时直接复用 
  - 静态提升
  - 事件监听缓存
  - SSR优化

 	`vue3`在兼顾`vue2`的`options API`的同时还推出了`composition API`，大大增加了代码的逻辑组织和代码复用能力 

### Tree shaking

 `Tree shaking` 是一种通过清除多余代码方式来优化项目打包体积的技术，专业术语叫 `Dead code elimination` 。

 `Tree shaking`是基于`ES6`模板语法（`import`与`exports`），主要是借助`ES6`模块的静态编译思想，在编译时就能确定模块的依赖关系，以及输入和输出的变量 

- 编译阶段利用`ES6 Module`判断哪些模块已经加载
- 判断那些模块和变量未被使用或者引用，进而删除对应代码

## 问题

### Vue在初始化前做的事

​		注册像`_updata()`、`_render()`这些函数。

```js
function Vue(options) {
  //初始化
  this._init(options) //初始化状态、渲染模板
}

initMixin(Vue) //_init、 $mount
//添加生命周期
lifecycleMixin(Vue) //注册_updata()

renderMixin(Vue) //注册_render()

stateMixin(Vue) //$nextTick

//全局方法： Vue.mixin() Vue.component() Vue.extend()
initGlobalApi(Vue)
```

### new Vue()

- 合并处理options

- 初始化状态,初始化 props/data/method/watch/methods
  - 初始化props/data/computed/watch/methods，比如监听劫持data数据，将data和method属性代理到实例上。
  - 订阅生命周期函数
- 页面挂载
  -  解析HTML为ast语法树 
  - ast对象转换成render字符串
  - render字符串转换成render函数
  - 执行render函数生成vnode
  - 调用_updata--->patch--->更新渲染DOM

```js
Vue.prototype._init = function(options) {
    let vm = this
    //这里合并主要是 mixins 或 extends 的方法，订阅收集生命周期函数等
    vm.$options = mergeOptions(Vue.options, options) 
    console.log(vm.$options)
    callHook(vm, 'beforeCreated') //触发生命周期函数
    //初始化状态,初始化props/data/computed/watch/methods
    initState(vm)
    callHook(vm, 'created')
    //渲染模板,_updata/_render
    if(vm.$options.el) {
        vm.$mount(vm.$options.el)
    }
}
```

渲染模板过程：

- 解析HTML，生成ast语法树
- Ast语法树转换成render字符串
- render字符串转换成render函数（with（this）{}）
- render函数生成vnode
- vnode渲染为真实的DOM

```js
//渲染模板
Vue.prototype.$mount = function() {
    let vm = this, options = vm.$options
    const el = document.querySelector(options.el)
    const elStr = el.outerHTML
    vm.$el = el //旧Dom
    //判断options的el必须有， 其次判断是否有render、之后是template、没有用el获取outerHTML
    if(!options.render) {
        if(!options.template) {
            //变成ast语法树
            let render = compoleToFunction(elStr)
            //变成render()
            //（1）将render函数变成vnode （2）将vnode变成真是的dom
            options.render = render 
        }
    }
    //挂载组件
    mounteComponent(vm, elStr)
}
```



```js
//_render  将render函数生成vnode， _c, _s, _v
//_updata  通过vnode更新DOM
export function mounteComponent(vm, el) {
  callHook(vm, 'beforeMount')
  //(1) _render: 将rendr函数变成vnode
  //vm._updata(vm._render())
  let updataComponent = () => {
    vm._updata(vm._render())
  }
  new Watcher(vm, updataComponent, () => {
    callHook(vm, "updated")
  }, true)

  callHook(vm, 'mounted')
}
```

### 为什么需要nextTick

​	如果每次有数据改变都更新一次，对性能影响很大。所以就需要对更新_updata()进行防抖处理。

​	 `Vue` 在更新 `DOM` 时是异步执行的。当数据发生变化，`Vue`将开启一个异步更新队列，视图需要等队列中所有数据变化完成之后，再统一进行更新 

​	nextTick就是一个异步方法，一个微任务。也就说他会等到页面所有数据更新完再执行

```js
Watcher{
  ...
  updata() {
    //this.getter()
    //执行太过频繁，防抖一下
    queueWatcher(this)
  }
   ...
}

let queue = [], has = {}, pending = false
function flushWatcher() {
  queue.forEach(item => {
    item.run()
    item.cb() 
  })
  queue = []
  has = {}
  pending = false
}
function queueWatcher(watcher) {
  let id = watcher.id
  if(has[id] == null) {
    queue.push(watcher)
    has[id] = true
    if(!pending) {
      nextTick(flushWatcher)
    }
    pending = true
  }
}
//nextTick就是一个异步方法，一个微任务。也就说他会等到页面所有数据更新完再执行
export function nextTick(cb) {
  callback.push(cb)
  if(!pending) {
    timerFunc() //异步方法
    pending = true
  }
}
```

### 生命周期

- `beforeCreate`->`setup`

- `created `-> `setup`

- `beforeMount` -> `onBeforeMount`

- `mounted` -> `onMounted`

- `beforeUpdate `-> `onBeforeUpdate`

- `updated `-> `onUpdated`

- `beforeDestroy `-> `onBeforeUnmount`

- `destroyed `-> `onUnmounted`

   	其中 vue3中的setup相当于vue2中beforeCreate 与created 但是的执行在beforeCreate 与created之前，所以setup无法使用 data 和 methods 中的数据和方法,即无法操作this,setup中的this等于 undefined,

### 首屏加载优化

- 原因

  - **网络延迟**
  - **资源文件体积过大**
  - **资源是否重复发送请求**
  - **加载脚本时，渲染内容堵塞**

- 解决方案

  - **减小入口文件体积大小**

    - 路由懒加载，采用加载动态路由的方法

      ```js
      //以函数的形式加载路由，这样就可以把各自的路由文件分别打包，只有在解析给定的路由时，才会加载路由组件 
      routes:[ 
          path: 'Blogs',
          name: 'ShowBlogs',
          component: () => import('./components/ShowBlogs.vue')
      ]
      ```

  - **静态资源本地缓存**

    后端返回资源问题：

    - 采用`HTTP`缓存，设置`Cache-Control`，`Last-Modified`，`Etag`等响应头
    - 采用`Service Worker`离线缓存

    前端合理利用`localStorage`

  - **UI框架按需加载**

  - **图片资源进行压缩**

  - **组件重复打包**

    假设`A.js`文件是一个常用的库，现在有多个路由使用了`A.js`文件，这就造成了重复下载

    解决方案：在`webpack`的`config`文件中，修改`CommonsChunkPlugin`的配置

    ```
    minChunks: 3
    ```

    `minChunks`为3表示会把使用3次及以上的包抽离出来，放进公共依赖文件，避免了重复加载组件

  - **开启Gzip压缩**

    拆完包之后，我们再用`gzip`做一下压缩 安装`compression-webpack-plugin`

    ```
    cnmp i compression-webpack-plugin -D
    ```

    在`vue.congig.js`中引入并修改`webpack`配置

    ```json
    const CompressionPlugin = require('compression-webpack-plugin')
    
    configureWebpack: (config) => {
            if (process.env.NODE_ENV === 'production') {
                // 为生产环境修改配置...
                config.mode = 'production'
                return {
                    plugins: [new CompressionPlugin({
                        test: /\.js$|\.html$|\.css/, //匹配文件名
                        threshold: 10240, //对超过10k的数据进行压缩
                        deleteOriginalAssets: false //是否删除原文件
                    })]
                }
            }
    ```

    在服务器我们也要做相应的配置 如果发送请求的浏览器支持`gzip`，就发送给它`gzip`格式的文件 我的服务器是用`express`框架搭建的 只要安装一下`compression`就能使用

    ```
    const compression = require('compression')
    app.use(compression())  // 在其他中间件使用之前调用
    ```

  - **使用SSR**

### 常见的修饰符

- 表单修饰符

  - lazy    光标离开标签的时候，才会将值赋予给`value` 
  - trim     自动过滤用户输入的首空格字符，而中间的空格不会过滤 
  - number

- 事件修饰符

  - stop

  - prevent

  - self

  - once

  - capture

  - passive

     在移动端，当我们在监听元素滚动事件的时候，会一直触发`onscroll`事件会让我们的网页变卡，因此我们使用这个修饰符的时候，相当于给`onscroll`事件整了一个`.lazy`修饰符 

  - native

- 鼠标按钮

  - left
  - right
  - middle

- 键值修饰符

- v-bind修饰符

### Vue3和Vue2的区别

- 速度更快
- 体积减少
- 更易维护
- 更接近原生
- 更易使用

### Vue如何做的性能提升

#### 编译阶段

- diff算法优化

  增加了静态标记，对那些不更新的节点不再需要进行比较

- 静态提升

  `Vue3`中对不参与更新的元素，会做静态提升，只会被创建一次，在渲染时直接复用 

- 事件监听缓存

- SSR优化

#### 源码体积

#### 响应式系统

​	 `vue3`采用`proxy`重写了响应式系统，因为`proxy`可以对整个对象进行监听，所以不需要深度遍历 



### Vue3.2 + TS + Vite

环境： Node16.0+

