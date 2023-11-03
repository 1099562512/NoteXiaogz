### nextTick机制

​	Vue实时响应式并不是数据发生变化后DOM立即变化。而是等**同一事件循环**中所有数据变化完成之后，再同一进行视图更新。

### v-if和v-show的区别

- v-if切换过程中会造成DOM的销毁和渲染，使得组件内部事件监听器和子组件进行销毁和重建。v-if是惰性的，如果一开始为false就不会渲染
  - ​v-show无论如何都会渲染，只是通过css的display进行显示隐藏。


### 如何获取v-if的DOM元素

​	需要使用nextTick进行获取

​	此处有个坑，就是如果v-if的元素是个组件，那么必须是个同步的组件，如果是异步加载的组件(defineAsyncComponent)会出现第一次获取不到DOM的情况。

```js
 state[key] = active !== undefined ? active : !state[key] //显示v-if元素
 nextTick(() => {
   console.log(document.querySelector(`.${key}`))
 })
```

