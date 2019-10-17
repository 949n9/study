> 页面刷新的时候不丢失vuex的数据,就使用`vue-persistedstate`吧

可以使用 [vuex-persistedstate](https://github.com/robinvdvleuten/vuex-persistedstate) 。这是一个用于 `vuex` 在页面刷新之间处理和存储状态的插件。

```js
import createPersistedState from 'vuex-persistedstate'

const store = new Vuex.Store({
  // ...
  plugins: [
    createPersistedState()
  ]
})
```

在new一个Store的时候就可以用一个plugins来使用这个插件。

以上是将状态保存在 localStorage ，也可以使用` js-cookie`这个工具 将状态保存在cookie。

