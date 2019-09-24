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

应用场景

自定义事件传值，要把数据暴露出去首先要掌握一个api `watch`

```html
//子组件
watch :{
	//监听currentValue的变化
  currentValue(value){
  		this.$emit("自定义事件名"， value)
  }
}

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

1.子组件内部的值发生变化：

2.通过触发一个$emit（'自定义事件名'，’自定义事件所需要传出的值）



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