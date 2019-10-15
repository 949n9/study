# Vue-router

传统的页面应用，是用一些超链接来实现页面切换和跳转的。在vue-router单页面应用中，则是路径之间的切换，也就是组件的切换。**路由模块的本质 就是建立起url和页面之间的映射关系**。



## 原理

Router中有两种模式：

* Hash (#锚点)
	`hash`模式的原理就根据Dom中的事件监听器，对`onload`事件中的`location.hash`进行监听，然后当路由跳转的时候,对`onhashchange`事件进行监听，同样获取路由地址，从而建立地址对路由的映射关系。
	
	```js
	            window.addEventListener("load",()=>{
	                this.history.current = location.hash.slice(1)
	                // console.log(this.history.current)
	            })
	            window.addEventListener("hashchange",()=>{
	                // console.log(this.history.current,"----")
	                this.history.current = location.hash.slice(1)
	                // console.log(this.history.current)
	            })
	//在事件监听事件中的事件不需要加on，例如onclick,只需要写click就可以了。
	```
	
	
	
* History 模式

  Histroy模式的url中没有#，一般在应用中使用比较多，和hash模式一样，也是对`onload`事件进行监听，但是监听的是`location.pathname`,当地址变更时，对`popstate`进行监听，建立映射关系

  ```js
              location.pathname ? "" : location.pathname = "/"
              window.addEventListener("load",()=>{
                  this.history.current = location.pathname
              })
              window.addEventListener("popstate",()=>{
                  this.history.current = location.pathname
              })
  ```

  

知识点： 

[HTML DOM 事件](https://www.runoob.com/jsref/dom-obj-event.html)

[HTML DOM addEventListener() 方法](https://www.runoob.com/jsref/met-element-addeventlistener.html)





<img src="https://tva1.sinaimg.cn/large/006y8mN6ly1g73q4x5m4rj30jy13sn43.jpg" alt="image-20190918155554500" style="zoom: 50%;" />





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

一个“路径参数”使用冒号 `:` 标记。当匹配到一个路由时，参数值会被设置到 `this.$route.params`，可以在每个组件内使用。于是，我们可以更新 `User` 的模板，当我们访问`user/2222`输出当前用户的 ID：

```js
created(){
  console.log(this.$route.params)
  // {'id': '2222'}
}
```

> 需要比较注意的一点，当你在同个页面进行跳转，即`page/111` 跳转到`page/222`时，会取不到id值，因为Vue的机制是跳转到同一个路由时，会复用这个组件，所以生命钩子不会再执行。解决办法就是在`watch`中取动态路由的id。或者使用导航守卫。

```js
        watch: {
        		//next是跳转的页面，pre是上一个页面
            ’$route‘(to, from){
                console.log(to);
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

导航守卫：beforeRouteUpdate

```js
 beforeRouteUpdate (to, from, next) {
    // react to route changes...
    // don't forget to call next()
  }
```

**多段路径参数**

|             模式              |         匹配路径          | $route.parmas                          |
| :---------------------------: | ------------------- | -------------------------------------- |
|        /user/:username        | /user/evan          | `{ username: 'evan' }`                 |
| /user/:username/post/:post_id | /user/evan/post/123 | `{ username: 'evan', post_id: '123' }` |

除了 `$route.params` 外，`$route` 对象还提供了其它有用的信息，例如，`$route.query` (如果 URL 中有查询参数)、`$route.hash` 等等。