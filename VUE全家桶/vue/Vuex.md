

## State

Vuex 使用**单一状态树**——是的，用一个对象就包含了全部的应用层级状态。至此它便作为一个“唯一数据源 ([SSOT](https://en.wikipedia.org/wiki/Single_source_of_truth))”而存在。这也意味着，每个应用将仅仅包含一个 store 实例。

但是当项目变得很大的时候，可以采用模块化`Module`的方法把`store`分割成很多模块。详见后面的`Module`模块。

```js
const store = new Vuex.Store({
		state:{
			count: 1	
	}
})

export default store
```
要想在全局使用`store`，需要在`vue`的实例中注册`store`

```js
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'

Vue.config.productionTip = false

new Vue({
  router,
  store,
  render: h => h(App)
}).$mount('#app')
//全局注册store

```



### 使用State

* 直接使用差值表达式，通过`$store.state.count`就可以获取到值。

```vue
<template>
<span>{{ $store.state.count }}</span>
</template>
```

* 用计算属性存起来

```js
<div> num: {{ count1 }}</div>


export default {
  //....
  computed: {
    count(){
      return this.$store.state.count
    }
  }
}
```
* mapState



### `mapState` 辅助函数

当一个组件需要获取多个状态时候，将这些状态都声明为计算属性会有些重复和冗余。为了解决这个问题，我们可以使用 `mapState` 辅助函数帮助我们生成计算属性，让你少按几次键：

```js
// 在单独构建的版本中辅助函数为 Vuex.mapState
import { mapState } from 'vuex'

export default {
  // ...
  computed: mapState({
    // 箭头函数可使代码更简练
    // 键值对，值是一个函数，用es6写法隐式返回。
    count: state => state.count,

    // 传字符串参数 'count' 等同于 `state => state.count`
    countAlias: 'count',

    // 为了能够使用 `this` 获取局部状态，必须使用常规函数
    countPlusLocalState (state) {
      return state.count + this.localCount
    }
  })
}
```
还可以进行简写，用`[ ]`简化，前提是store中的变量名，和组件中的变量名一致。

```JS
    computed:{
        count1() {
          return this.$store.state.count
        },
        //第一种写法
        ...mapState({
          count2: state => state.count,
          list1: state => state.list
        }),
        //第二种写法，键值对，键是组件中所声明的名字，值是函数，接收一个参数state，隐式返回所需要的变量
        ...mapState(['count','list'])
      	//第三种写法，一般都采用这一种写法。
  }
```



想要修改State中的数据，就必须显式提交一个`Commit`



## Mutation





### Mutation 必须是同步函数

因为`Vue`需要捕获每一条`Mutation`的前后状态，如果使用了异步函数，那么就捕获不到异步函数什么时候执行回调。

> 官方原话： 因为当 `mutation` 触发的时候，回调函数还没有被调用，`devtools` 不知道什么时候回调函数实际上被调用——实质上任何在回调函数中进行的状态的改变都是不可追踪的。

**用法**

```js
export default new Vuex.Store({
  state: {
    count : 0
  },
  mutations: {
    ADD_COUNT: function (state,payload) {
        state.count += payload
    }
  }
})

```

每一条mutations 都会有一个事件类型 (type) 和 一个 回调函数 (handler)
上述例子的type就是'ADD_COUNT'，后面跟的就是回调函数，回调函数接收两个参数：

* state
* payload(载荷)，就是需要传递的参数，通常这个参数应该是一个对象，这样可以包含更多的字段，使得mutations更好读

![image-20190924103844545](https://tva1.sinaimg.cn/large/006y8mN6ly1g7aeos6lh8j30d606et8y.jpg)

```js
// ...
mutations: {
  ADD_COUNT: function (state,payload) {
    state.count += payload.val
  }
}
```

```js
$store.commit('ADD_COUNT',{
		val: 10
})
```



## Action

Action 类似于 mutation，不同在于：

- Action 提交的是 mutation，而不是直接变更状态。
- Action 可以包含任意异步操作。





## Getter

