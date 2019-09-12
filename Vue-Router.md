# Vue-Router

## 动态路由的匹配

我们经常需要把某种模式匹配到的所有路由，全都映射到同个组件。例如，我们有一个 `User` 组件，对于所有 ID 各不相同的用户，都要使用这个组件来渲染。那么，我们可以在 `vue-router` 的路由路径中使用“动态路径参数”(dynamic segment) 来达到这个效果：

```js
    {
      path: '/users/:id',
      name: 'users',
      component: () => import('./views/users.vue')
    }
```

一个“路径参数”使用冒号 `:` 标记。当匹配到一个路由时，参数值会被设置到 `this.$route.params`，可以在每个组件内使用。于是，我们可以更新 `User` 的模板，输出当前用户的 ID：

```js
created(){
  console.log(this.$route.params)
  // {'id': '2222'}
}
```

> 需要比较注意的一点，当你在同个页面进行跳转，即`page/111` 跳转到`page/222`时，会取不到id值，因为Vue的机制是跳转到同一个路由时，会复用这个组件，所以生命钩子不会再执行。解决办法就是在`watch`中取动态路由的id

```
        watch: {
        		//next是跳转的页面，pre是上一个页面
            $route(next, pre){
                console.log(next);
            }
        }
        /* next的内容
        	fullPath: "/page/2222"
          hash: ""
          matched: [{…}]
          meta: {}
          name: "page"
          params: {id: "2222"}
          path: "/page/2222"
          query: {}
          __proto__: Object */
```

