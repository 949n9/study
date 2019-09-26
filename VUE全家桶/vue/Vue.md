# Vue

## 动态路由的匹配

## 组件通信

### 父组件向子组件传值

1. 在子组件内声明props
```js
props: {
	传入的属性名：{
		type : String （类型声明）
		require : true	（是否必须）
	}
}
```
2. 在父组件中的标签绑定属性值
```html
<my-component :（属性名）='数据'  ><my-component/>
```

### 子组件向父组件传值

```html
//子组件
<template>
  <button  @click="$emit('自定义事件'，值)" >click this!</button>
</template>
//子组件也可以这么写
<template>
  <button  @click="someChange" >click this!</button>
</template>

<scirpt>
export defalut {
  		...
  		methods: {
  			someChange(){
  				this.$emit('自定义事件名'，值)
	 		}
  	}
  }
</scirpt>

```

然后父组件接收自定义事件名和值，值默认在第一个参数，或者可以用`$event`取到。
当在父级组件监听这个事件的时候，我们可以通过` $event `访问到被抛出的这个值   --- 官方案例

```html
<blog-post
  ...
  v-on:enlarge-text="postFontSize += $event"
></blog-post>
```

或者这样：

```js
//父组件
<my-component @自定义事件名></my-component>

<script>
export default{
		methods: {
				自定义事件名(value){
						console.log("子组件触发的事件"， value)
				}
		}
}
</script>
```



当然我们还可以用到`watch`这个api ，用来监控子组件里面的某个值，如果某个值发生变化的时候，自动给父组件发送自定义事件。

```html
<script>
  export default {
    watch :{
      val: function(newVal,oldVal){
        this.$emit('自定义事件'，值)
      }
    }
  }
</script>
```



### 隔代传值，其他传值方法汇总



## 插槽







## 实例的生命周期

beforeCreate:

解释：组件实例化之前，也就是初始化组件之前

表现：`data`和`methods`中的东西还取不到值

created：

解释： 组件实例化完成，但是还没有编译模板

表现：可以取`data`和`methods`中的值。但是

beforeMount

解释：模板已经编译完成了，但是还没有渲染。

表现： 通过`document.getElementById()`获取不到值

mounted：

解释：模板已经渲染完成了。

表现：可以通过`document.getElementById()`获得模板元素

beforeUpdate

解释：在数据更新之前

表现：`data`中的值已经改变了，但是模板中的值还没有改变

updated

解释：虚拟DOM重新渲染，并且应用更新

表现：`data`中的值和模板中的值都完成了更新

beforeDestroy

解释：在组件被销毁之前

表现：如果页面存在定时器或者其他子组件，监听器，可以在这个钩子上清除

Destroyed

解释： 销毁完毕