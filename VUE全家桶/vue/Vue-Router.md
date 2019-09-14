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

