

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
//通过commit显式触发
```



当然Mutation也有辅助函数`mapMutations`，与`mapState`一样也有两种写法。

```js
methods: {
 	 add(val) {
    	this.$store.commit('ADD_COUNT', {val})
  	},
   ...mapMutations({
      add1: 'ADD_COUNT'
		}),
   ...mapMutations(['ADD_COUNT'])
}
```



### Mutation 需遵守 Vue 的响应规则

既然 Vuex 的 store 中的状态是响应式的，那么当我们变更状态时，监视状态的 Vue 组件也会自动更新。这也意味着 Vuex 中的 mutation 也需要与使用 Vue 一样遵守一些注意事项：

1. 最好提前在你的 store 中初始化好所有所需属性。
2. 当需要在对象上添加新属性时，你应该

- 使用 `Vue.set(obj, 'newProp', 123)`, 或者

- 以新对象替换老对象。例如，利用 stage-3 的[对象展开运算符](https://github.com/sebmarkbage/ecmascript-rest-spread)我们可以这样写：

  ```js
  state.obj = { ...state.obj, newProp: 123 }
  ```

## Action

Action 类似于 mutation，不同在于：

- Action 提交的是 mutation，而不是直接变更状态。
- Action 可以包含任意异步操作。

```js
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    }
  }
})
//一个简单的action的例子但是我们一般都会采用解构语法
```

Action 函数接受一个与 store 实例具有相同方法和属性的 context 对象，因此你可以调用 `context.commit` 提交一个 mutation，或者通过 `context.state` 和 `context.getters` 来获取 state 和 getters。当我们在之后介绍到 [Modules](https://vuex.vuejs.org/zh/guide/modules.html) 时，你就知道 context 对象为什么不是 store 实例本身了。

使用解构语法：

```js
actions: {
  increment ({ commit }) {
    commit('increment')
  }
}
```

### 分发 Action

```js
store.dispatch('increment')
```

乍一眼看上去感觉多此一举，我们直接分发 mutation 岂不更方便？实际上并非如此，还记得 **mutation 必须同步执行**这个限制么？Action 就不受约束！我们可以在 action 内部执行**异步**操作：

```js
actions: {
  incrementAsync ({ commit }) {
    setTimeout(() => {
      commit('increment')
    }, 1000)
  }
}
```

Actions 支持同样的载荷方式和对象方式进行分发：

```js
// 以载荷形式分发
store.dispatch('incrementAsync', {
  amount: 10
})

// 以对象形式分发
store.dispatch({
  type: 'incrementAsync',
  amount: 10
})
```



###  组合 Action

Action 通常是异步的，那么如何知道 action 什么时候结束呢？更重要的是，我们如何才能组合多个 action，以处理更加复杂的异步流程？

首先，你需要明白 `store.dispatch` 可以处理被触发的 action 的处理函数返回的 Promise，并且 `store.dispatch` 仍旧返回 Promise：

```js
actions: {
  actionA ({ commit }) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        commit('someMutation')
        resolve()
      }, 1000)
    })
  }
}
```

然后就可以：

```js
store.dispatch('actionA').then(() => {
  // ...
})
```

再另一个actions也可以：

```js
actions: {
  // ...
  actionB ({ dispatch, commit }) {
    return dispatch('actionA').then(() => {
      commit('someOtherMutation')
    })
  }
}

```



> 一个 `store.dispatch` 在不同模块中可以触发多个 action 函数。在这种情况下，只有当所有触发函数完成后，返回的 Promise 才会执行。



## Getter

有时候我们需要从 store 中的 state 中派生出一些状态，例如对列表进行过滤并计数。

Vuex 允许我们在 store 中定义“getter”（可以认为是 store 的计算属性）。就像计算属性一样，getter 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算。

Getter 接受 state 作为其第一个参数：

```js
const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
  }
})
```

### 通过属性访问

Getter 会暴露为 `store.getters` 对象，你可以以属性的形式访问这些值：

```js
store.getters.doneTodos // -> [{ id: 1, text: '...', done: true }]
```

### getter的第二个参数
getter的第二个参数就是其他getter，可以获取到其他getter的值，如下：
```js
getters: {
  // ...
  doneTodosCount: (state, getters) => {
    return getters.doneTodos.length
  }
}
```



### `mapGetters` 辅助函数

`mapGetters` 辅助函数仅仅是将 store 中的 getter 映射到局部计算属性：

```js
import { mapGetters } from 'vuex'

export default {
  // ...
  computed: {
  // 使用对象展开运算符将 getter 混入 computed 对象中
    ...mapGetters([
      'doneTodosCount',
      'anotherGetter',
      // ...
    ])
  }
}
```
> 如果你想将一个 getter 属性另取一个名字，使用对象形式
```js
...mapGetters({
  // 把 `this.doneCount` 映射为 `this.$store.getters.doneTodosCount`
  doneCount: 'doneTodosCount'
})
```

