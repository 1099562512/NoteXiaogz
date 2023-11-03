## Vue2.0

### 全局API

### options

#### watch

```js
{
	watch: {
        'a'(newV, oldV) {}
        'a'[(), ()]
        'a'{
            handler() => {}
        }
        'a': 'aa'
	}
}
```

### 实例property

#### vm.$attrs

类型：`{ [key: string]: string } `

​	包含了父作用域不被prop的属性（不包含`style`和`class`）， 当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (`class` 和 `style` 除外)，并且可以通过 `v-bind="$attrs"` 传入内部组件——在创建高级别的组件时非常有用。 

```js
//父组件
<test :test1="name" :visible.sync="isShowDialog" @close="closeDialog"></test>

//子组件
<el-dialog title="参数传递测试" v-bind="$attrs" v-on="$listeners">
props: {
  test1: String
},
console.log(this.$attrs) // {visible: false}

//子组件的props没有接受test1属性
console.log(this.$attrs) // {test1: 'home1', visible: false}

```

#### vm.$listeners

类型：` { [key: string]: Function | Array<Function> } `

​	 包含了父作用域中的 (不含 `.native` 修饰器的) `v-on` 事件监听器。它可以通过 `v-on="$listeners"` 传入内部组件 

### 通信

#### provide / Inject

​	 允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在其上下游关系成立的时间里始终生效 。 `provide` 和 `inject` 绑定并不是可响应的。 

- provide 	Object | （）=>  Object
- inject         Array<string> |  { [key: string]: string | Symbol | Object } 

```js
//通过provide向子孙组件注入祖先组件的实例, 避免this.$parent.$parent
provide() {
  return {
    ancestor: this //可以是对象、变量、函数
  }
}

inject: ['ancestor']
inject: {
    alias: 'ancestor'
}
//调用 this.alias
```

​	此时后代组件修改`isShowA`，祖先组件的`isShowA`也会随之改变。

#### $parent / $children

- $children

  获取子组件实例列表，数组类型可以用组件名称进行区分，获取组件名称

  ```
  this.$children[0].$options.name
  ```

- $parent

  获取组件的父组件实例

#### v-model 双向绑定

​	适用于父子组件需要同步且频繁的数据，本质还是props/emits。

 	model选项允许一个自定义组件在使用 `v-model` 时定制 prop 和 event。默认情况下，一个组件上的 `v-model` 会把 `value` 用作 prop 且把 `input` 用作 event 

```js
//父组件
<child-b v-model="isShowB"></child-b>

//子组件
<a-model :visible="isShowB" @cancel="handleCancel"></a-model>
{
    model: {
       prop: "isShowB",
       evnet: "changeShowB"
    },
    props: {
        isShowB：Boolean
    },
    methods: {
       handleCancle() {
           this.$emit('changeShowB', false)
       }
    }
}

```

#### props/emit

​	适用于简单的父子组件的数据交互

```js
//父组件
<children :val="" @accept=""></children>

//子组件
props: {
    val: Object | Array | Number...
}
this.$emit('accept', payload)
```

#### eventBus

- on		观察者（obsever）订阅某一事件

- emit     触发某一事件，通知订阅该事件的所有观察者 

- off        观察者取消订阅某一事件

  下面是自己写的发布/订阅模式

```js
class EventBus {
  constructor() {
    // 事件队列
    this._event = new Map() // Map(1) {'sendMessage' => Array(2)}
  }
  on (event, obsever) {
    //一个event可以对应多个obsever
    let curEvent = this._event.get(event)
    if (!curEvent){
      this._event.set(event, [obsever])
    } else {
      curEvent.push(obsever)
    }
  }
  off (event, obsever) {
    let curEvent = this._event.get(event)
    if (curEvent){
      this._event.delete(event)
    }
  }
  emit (event, ...arg) {
    console.log(this._event);
    let curEvent = this._event.get(event)
    if( curEvent ) {
      for(let obsever of curEvent) {
        obsever.call(this, arg) //传递参数
      }
    } else {
      console.log('不存在或已经取消订阅');
    }
  }
}
```

​	或者利用Vue实例的$on、$emit、$off

```js
//vueBus.js
import Vue from "vue";
export default new Vue()

import bus from '@util/vueBus'
bus.$on('sendMessage', () => {
    this.isShowDialog = false
})
bus.$emit('sendMessage', "name")
```

#### $attrs / $listeners

- $attrs

   包含了父作用域中不作为 prop 被识别 (且获取) 的 attribute 绑定 (`class` 和 `style` 除外)。 

- $listeners

#### ref / $refs

​	 如果在普通的 DOM 元素上使用，引用指向的就是 DOM 元素；如果用在子组件上，引用就指向组件实例，可以通过实例直接调用组件的方法或访问数据 

```js
<test :test1="name" ref="test" v-model="isShowDialog" @close="closeDialog"></test>
const test = this.$refs['test']
test.changeName()
```

#### vuex

#### 生命周期

##### updated

### 插槽

```vue
<navigation-link url="/profile">
  Your Profile
</navigation-link>

<a
  v-bind:href="url"
  class="nav-link"
>
  <slot></slot>
</a>
```

具名插槽

```vue
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  ...
</div>

<base-layout>
  <template v-slot:header>
    <h1>Here might be a page title</h1>
  </template>
  ...
</base-layout>
```

插槽作用域， 父级的插槽内容中可用子级的开放得内容

```vue
<span>
  <slot v-bind:user="user">
    {{ user.lastName }}
  </slot>
</span>

<current-user v-slot="{ user: person }">
  {{ person.firstName }}
</current-user>
```

