## Vue3.0

### 全局API

#### createApp

 根组件对象作为参数， 返回一个提供应用上下文的应用实例。应用实例挂载的整个组件树共享同一个上下文。

```vue
import { createApp } from 'vue'
import App from './App.vue'
const app = createApp(App)
app.use(Antd).use(store).use(router).mount('#app')
```

#### component

​	 注册或检索全局组件。注册还会使用给定的 `name` 参数自动设置组件的 `name`。 

#### directive

- 参数

  - `{string} name ` 指令名称
  - `{Function | Object} [definition]`指令定义

- 返回值

  - 如果传入 `definition` 参数，则返回应用实例。
  - 如果不传入 `definition` 参数，则返回指令定义。

- 用法

  注册或检索全局指令

```js
// 注册
app.directive('my-directive', {
  // 指令具有一组生命周期钩子：
  // 在绑定元素的 attribute 或事件监听器被应用之前调用
  created() {},
  // 在绑定元素的父组件挂载之前调用
  beforeMount() {},
  // 在绑定元素的父组件挂载之后调用
  mounted() {},
  // 在包含组件的 VNode 更新之前调用
  beforeUpdate() {},
  // 在包含组件的 VNode 及其子组件的 VNode 更新之后调用
  updated() {},
  // 在绑定元素的父组件卸载之前调用
  beforeUnmount() {},
  // 在绑定元素的父组件卸载之后调用
  unmounted() {}
})

// 注册 (函数指令)
app.directive('my-directive', () => {
  // 这将被作为 `mounted` 和 `updated` 调用
})
```

#### config

**globalProperties**

​	 添加一个可以在应用的任何组件实例中访问的全局 property。组件的 property 在命名冲突时具有优先权。 

```js
// 之前 (Vue 2.x)
Vue.prototype.$http = () => {}

// 之后 (Vue 3.x)
const app = createApp({})
app.config.globalProperties.$http = () => {}
```

#### use

​	 安装 Vue.js 插件。 返回实例本身。

#### mount

​	 所提供 DOM 元素的 `innerHTML` 将被替换为应用根组件的模板渲染结果。 

### 响应式API

#### refs

> **toRefs**

 	将**响应式对象**转换为普通对象，**其中结果对象的每个 property 都是指向原始对象相应 property 的** [`ref`](https://v3.cn.vuejs.org/api/refs-api.html#ref)。 

```js
const state = reactive({
  message: "hello"
})
console.log(state); //Proxy {message: 'hello'}
console.log({...state}); //{message: 'hello'} state结构后对象属性失去响应特性
console.log(toRefs(state)); //{message: ObjectRefImpl}  toRefs赋予对象属性响应性
```

#### reactive

​	返回对象的响应式副本，基于Proxy实现，响应式转换是“深层”的——它影响所有嵌套 property。

### 监听和计算属性

#### watch  /  watchEffect

##### watch

​	需指定要监听的属性

#### computed

​	接受一个 getter 函数，并根据 getter 的返回值返回一个不可变的响应式 [ref](https://v3.cn.vuejs.org/api/refs-api.html#ref) 对象

```js
const count = ref(1)
const plusOne = computed(() => count.value + 1)
console.log(plusOne.value) // 2

plusOne.value++ // 错误
```

### 通信

#### Provide / Inject

​	`provide` 和 `inject` 启用依赖注入。这两者只能在使用当前活动实例的 [`setup()`](https://v3.cn.vuejs.org/api/composition-api.html#setup) 期间被调用。

```js
//祖先组件
import { provide } from 'vue';
const isShowA = ref(false)
provide('isShowA', isShowA)

//后代组件
import { inject } from 'vue';
const isShowA = inject('isShowA')
```

​	此时后代组件修改`isShowA`，祖先组件的`isShowA`也会随之改变。

#### v-model 双向绑定

```js
//父组件
<child-b v-model:isShowB="isShowB"></child-b>

//子组件
<a-model v-model:isShowB="isShowB" @cancel="handleCancel"></a-model>
import {defineProps, defineEmits} from 'vue' //根据版本可省略
const props = defineProps({
    isShowB: Boolean
})
const emit = defineEmits(['update:isShowB'])
const handleCancel = () => {
    emit('update:isShowB', false)
}
```

#### props/emits

#### ref / definedExpose

父组件使用ref获取子组件暴露的方法和变量

```js
<base-map ref="myMap"></base-map>
<script setup>
  import { defineComponent, onMounted, toRefs, ref } from 'vue';
  import baseMap from './fleet/baseMap/index.vue';

  const myMap = ref()
  onMounted(() => {
    console.log('mounted');
    console.log(myMap.value); //注意此时子组件需要加载完成
  });
</script>

//子组件
const name = ref('123123')
const changeName = () => {
name.value = "hello world"
}
onMounted(() => {
console.log("12313");
})
defineExpose({
    name, 
    changeName
})
```

#### EventBus

#### vuex / pina

### 生命周期

- `beforeCreate`->`setup`
- `created `-> `setup`
- `beforeMount` -> `onBeforeMount`
- `mounted` -> `onMounted`
- `beforeUpdate `-> `onBeforeUpdate`
- `updated `-> `onUpdated`
- `beforeDestroy `-> `onBeforeUnmount`
- `destroyed `-> `onUnmounted`

### 渲染函数

#### vue 2.0

```js
//return {VNode}
createElement(elementName，{Object}，[])
```

第一个参数：{String|Object|Function} 可以是一个HTML标签，一个组件、一个异步组件或函数式组件

第二个参数：{Object}	一个与模板中attribute对应的数据对象。可选

第三个参数：{String|Array} 子级虚拟节点（VNodes）, 由createElement()构建而成，也可以使用字符串来生成“文本虚拟节点”。可选。

```js
 render: (h, params) => { 
   console.log(h);
   const handleEdit = () => {
     console.log(params);
   }
   const temp = [
     h('span', {
       domProps: {
         innerHTML: '12'
       }
     }),
     h('Button', {
       attrs: {
         type: 'primary',
         icon: 'md-create',
         size: 'small'
       },
       'class': {
         editRealShip: true
       },
       on: {
         click: handleEdit
       }
     })
   ]
   return h('div', temp)
 }
```



#### vue 3.0

### Vuex

```js
//批量引入组件
const path = require('path')
const files = require.context('@/components/home', false, /\.vue$/)
const modules = {}
files.keys().forEach(key => {
  const name = path.basename(key, '.vue')
  modules[name] = files(key).default || files(key)
})


// 导入文件内的 modules
const path = require("path");
const files = require.context("./modules", false, /\.js$/);
const modules = {};
files.keys().forEach(key => {
  const name = path.basename(key, ".js"); // 过滤掉 .js
  modules[name] = files(key).default || files(key);
});
```



```js
import {useStore} from 'vuex' // vue文件引入vuex
const sotre = useStore()

import store from '@/store/index'	//js文件引入vuex

//获取 state
store.state.属性
store.state.moduleName.属性
//调用 Mutation
store.commit(index/mutationName) //
//调用 action
store.distach(index/actionName)
```

