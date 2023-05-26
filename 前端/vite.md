## 依赖预构建

- 目的是为了兼容CommonJS和UMD规范
- 提升性能

> 构建过程

1. 如果是首次启动本地服务，那么vite会自动抓取源代码，从代码中找到需要预构建的依赖，最终对外返回类似下面的一个deps对象：

```js
{
  vue: '/path/to/your/project/node_modules/vue/dist/vue.runtime.esm-bundler.js',
  'element-plus': '/path/to/your/project/node_modules/element-plus/es/index.mjs',
  'vue-router': '/path/to/your/project/node_modules/vue-router/dist/vue-router.esm-bundler.js'
}
```

  具体实现就是，调用esbuild的build api，以index.html作为查找入口（entryPoints），将所有的来自node_modules以及在配置文件的optimizeDeps.include选项中
指定的模块找出来。

  来自node_modules中的模块依赖是需要预构建的。例如import ElementPlus from ‘element-plus’。因为在浏览器环境下，是不支持这种裸模块引用的（bare 
import）。另一方面，如果不进行构建，浏览器面对由成百上千的子模块组成的依赖，依靠原生esm的加载机制，每个的依赖的import都将产生一次http请求。面对大量的请求，浏览器是吃不消的。因此客观上需要对裸模块引入进行打包，并处理成浏览器环境下支持的相对路径或路径的导入方式。例如：import ElementPlus from ‘/path/to/.vite/element-plus/es/index.mjs’。

2. 查找到的依赖进行构建
   在上一步，已经得到了需要预构建的依赖列表。现在需要把他们作为esbuild的entryPoints打包就行了。

总结：先查找需要预构建的依赖（bare import通常是第三方包和optimizeDeps下的includes），然后将这些依赖作为entryPoints进行构建，构建完更新缓存。vite在启动时为提升速度，会检查缓存是否有效，有效的话会跳过预构建环节，缓存是否有效是对比缓存中的hash值和当前的hash值是否相同。


  首先vite会找到对应的依赖， 然后调用esbuild，将其他规范的代码转换成esmodule规范，然后放到当前目录下的node_modules/.vite/deps, 同时对esmodule规范的各个模块进行同一集成

  ```js
export { default as a } from './a.js'
//重写成
function a() {}
  ```

## postCss

工作内容: 保证css内容在各个浏览器上执行起来万无一失
css有很多新特性，为了浏览器的兼容性，可能需要做一些降级处理，添加前缀

我们写得css代码（嵌套函数、变量、高级特性等）-----> 【去将语法进行编译生成原生css，less sass等预处理器也可以做】-----> 再次对未来的高级语法继续降级 -----> 前缀补全 -----> 浏览器客户端

## 使用postCss

1. 安装依赖

```
yarn add postcss-cli postcss -D
```

2.  书写描述文件
    创建postcss.config.js, 看官网[postcss Github](https://github.com/postcss/postcss/blob/main/docs/README-cn.md)


打包后的静态资源为什么要有hash
浏览器是有一个缓存机制的，静态资源只要不改， 就会直接用缓存

## 前端性能优化

- 开发时构建速度优化
  - webpack cache-loader、 thread-loader
  - vite是按需加载
- 页面性能指标
  - 首屏加载时间
    - 懒加载
    - http优化：协商缓存和强缓存
      - 强缓存： 服务端给响应头追加一些字段（expires）， 客户端会记住这些字段，在expires（失效时间）没有到达之前，都不会重新发送请求，而是重新请求页面
      - 协商缓存：服务端根据资源更新情况决定是否用缓存还是重新请求
  - 页面中最大元素的一个时长
  - js逻辑
    - 注意副作用的清除： 比如setInterval、事件监听
    - 浏览器的帧率： 16.6ms去更新一次（执行js逻辑 以及 重排重绘...）requestAnimationFrame(绘制之前执行)、requestIdleCallback(绘制之后执行)

- 构建的优化： vite(rollup) webpack
  - 优化体积：压缩、treeshaking、图片资源压缩、cdn加载、分包 ...

### 分包策略

打包时，静态代码（文件名不会变）和动态代码进行分开打包。浏览器利用缓存可以减少请求数

## gzip压缩

服务端压缩
客户端收到压缩包 --> 解压缩

服务端读取gzip文件（.gz后缀）设置一个响应头 content-encoding --> gizp 告诉浏览器这是压缩过的文件，浏览器收到响应结果后就会对相应的文件进行解压（这会占用一定的解压缩时间，所以对于体积不大的文件不需要进行gzip压缩）

vite-plugin-compression插件

```ts
  import viteCompression from 'vite-plugin-compression'

  plugin: [
    viteCompression({
      threshold: 1024000 //对于1mb的文件进行压缩
    })
  ]
```

## 动态导入

vue的路由动态导入


## CDN加速

我们所有的依赖以及文件在我们进行打包后会放到我们的服务器上
将我们依赖的第三方模块全部写成cdn的形式，保证我们服务器上代码的一个小体积

vite-plugin-cdn-import插件

```ts
yarn add vite-plugin-cdn-import -D

improt viteCDNPlugin from 'vite-plugin-cdn-import'

plugin: [{
  viteCDNPlugin({
    modules: [{
      name: "vue",
      var: "Vue",
      path: "https://unpkg.com/vue@3.2.31" //需要注意引入的包版本需要与package.json的版本保持一致， jsdelivr.com
    }]
  })
}]
```

## 跨域

跨域【仅发生在浏览器】: 当A源浏览器的网页向B源的服务器地址请求对应信息，不满足同源策略，就会产生跨域，跨域请求默认情况下会被浏览器拦截（B源其实已经响应了，只不过被浏览器阻止了），除非对应的服务器出具标记允许A源获取B源的数据。

vite项目发送请求过程

浏览器先做拼接发送请求 --> Vite vite开发服务器监听拦截请求发现这个path有配置跨域代理策略，然后会根据策略的描述对象 **进行再次请求（这个请求是在开发服务器上的也就是Node环境里面的，不不存在同源策略，同源策略只有浏览器才有** --> 把请求结果再给到浏览器

- 开发环境: 我们一般利用 构建工具或脚手架或第三方库的proxy 代理配置，或者自己搭建开发服务器来处理
- 生产环境: 一般交给后端/运维处理
  - ngnix: 代理服务
  - 配置身份标记
    - Access-Control-Allow-Origin

## 创建vite项目

```
npm create vite@latest
yarn create vite 
pnpm crete vite
```

​	接下来就会要输入项目名称、需要的技术：如vue还是react、ts还是js，你也可以通过以下命令直接一步到位

```
# npm 6.x
npm create vite@latest my-vue-app --template vue

# npm 7+, extra double-dash is needed:
npm create vite@latest my-vue-app -- --template vue

# yarn
yarn create vite my-vue-app --template vue-ts

# pnpm
pnpm create vite my-vue-app --template vue-ts
```

目前支持的模板如下：

|             JavaScript              |                TypeScript                 |
| :---------------------------------: | :---------------------------------------: |
| [vanilla](https://vite.new/vanilla) | [vanilla-ts](https://vite.new/vanilla-ts) |
|     [vue](https://vite.new/vue)     |     [vue-ts](https://vite.new/vue-ts)     |
|   [react](https://vite.new/react)   |   [react-ts](https://vite.new/react-ts)   |
|  [preact](https://vite.new/preact)  |  [preact-ts](https://vite.new/preact-ts)  |
|     [lit](https://vite.new/lit)     |     [lit-ts](https://vite.new/lit-ts)     |
|  [svelte](https://vite.new/svelte)  |  [svelte-ts](https://vite.new/svelte-ts)  |

### unplugin-vue-components

​	自动导入组件

```js
// vite.config.js
import Components from 'unplugin-vue-components/vite';
import { AntDesignVueResolver } from 'unplugin-vue-components/resolvers';

export default {
  plugins: [
    /* ... */
    Components({
      resolvers: [AntDesignVueResolver()],
    }),
  ],
};
```

### 未找到path模块

```
npm install @types/node --save-dev
yarn add @types/node -D
```

### Cannot find module ‘XXX.vue‘ or its corresponding type declaration

**报错原因：vite使用的是ts，ts不识别 .vue 后缀的文件**

在`vite-env.d.ts`或`vite.d.ts`添加以下代码：

```ts
//vite-env.d.ts
declare module "*.vue" {
  import { DefineComponent } from "vue"
  const component: DefineComponent<{}, {}, any>
  export default component
}
```

### 按需引入AntD

### 按需引入Element UI

### 按需引入Echarts

### Svg

[README.zh_CN.md · main · mirrors / vbenjs / vite-plugin-svg-icons · GitCode](https://gitcode.net/mirrors/vbenjs/vite-plugin-svg-icons/-/blob/main/README.zh_CN.md)

```
yarn add vite-plugin-svg-icons -D
```

```js
// vite.config.js
import { createSvgIconsPlugin } from 'vite-plugin-svg-icons'

export default defineConfig({
  plugins: [
    vue(),
    Components({
      resolvers: [ AntDesignVueResolver() ]
    }),
    createSvgIconsPlugin({
      iconDirs: [resolve(process.cwd(), 'src/assets/svg')]
    })
  ],
  ...
})
```

在 src/main.js 内引入注册脚本

```
//main.js 引入
import 'virtual:svg-icons-register'
```

封装公共组件SvgIcon，`/src/components/SvgIcon.vue`

```vue
<template>
  <svg aria-hidden="true" class="svg-icon">
    <use :xlink:href="symbolId" :fill="color" />
  </svg>
</template>

<script setup>
  import { computed } from 'vue'
  const props = defineProps({
    prefix: {
      type: String,
      default: 'icon',
    },
    name: {
      type: String,
      required: true,
    },
    color: {  //需要注意的是，如果要color生效，需要将svg文件内的fill删除
      type: String,
      default: '#333',
    }
  })

  const symbolId = computed(() => `#${props.prefix}-${props.name}`)
</script>

<style scoped>
  .svg-icon {
    width: 100%;
    height: 100%;
    vertical-align: -0.15em;
    fill: currentColor;
    overflow: hidden;
  }
</style>
```

