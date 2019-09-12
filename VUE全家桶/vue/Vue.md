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



