


# Vue-persistedstate
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



# VueSticky

> vue的吸顶插件，可以实现多重吸顶效果

掘金链接：https://juejin.im/post/5dd13460e51d45401f07805e



- 安装：`npm install vue-sticky --save`

- 引入: `import VueSticky from "vue-sticky"`

- 使用：

- ```JS
  directives: {
      'sticky': VueSticky,
  },
  ```

- ```vue
  <ELEMENT v-sticky="{ zIndex: NUMBER, stickyTop: NUMBER, disabled: [true|false]}">
    <div> <!-- sticky wrapper, IMPORTANT -->
      CONTENT
    </div>
  </ELEMENT>
  
  ```

  

