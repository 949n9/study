# Vue

## component



### 组件通信

#### 父组件向子组件传值

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

####子组件向父组件传值

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



#### 在组件中使用v-model

一个组件上的 `v-model` 默认会利用名为 `value` 的 prop 和名为 `input` 的事件，但是像单选框、复选框等类型的输入控件可能会将 `value` 特性用于[不同的目的](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/checkbox#Value)。`model` 选项可以用来避免这样的冲突：

```js
Vue.component('base-checkbox', {
  model: {
    prop: 'checked',
    event: 'change'
  },
  props: {
    checked: Boolean
  },
  template: `
    <input
      type="checkbox"
      v-bind:checked="checked"
      v-on:change="$emit('change', $event.target.checked)"
    >
  `
})
```

注意你仍然需要在组件的 `props` 选项里声明 `checked` 这个 prop。

#### 隔代传值，其他传值方法汇总

##### provide  /  inject

**类型**：

- **provide**：`Object | () => Object`
- **inject**：`Array<string> | { [key: string]: string | Symbol | Object }` (字符串数组，或者对象)

> `provide` 和 `inject` 主要为高阶插件/组件库提供用例。并不推荐直接用于应用程序代码中。

这对选项需要一起使用，以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，并在起上下游关系成立的时间里始终生效。

`provide` 选项应该是一个对象或返回一个对象的函数。该对象包含可注入其子孙的属性。

`inject` 选项应该是：

- 一个字符串数组，或
- 一个对象，对象的 key 是本地的绑定名，value 是：
  - 在可用的注入内容中搜索用的 key (字符串或 Symbol)，或
  - 一个对象，该对象的：
    - `from` 属性是在可用的注入内容中搜索用的 key (字符串或 Symbol)
    - `default` 属性是降级情况下使用的 value

> 提示：`provide` 和 `inject` 绑定并不是可响应的。这是刻意为之的。然而，如果你传入了一个可监听的对象，那么其对象的属性还是可响应的。



**示例**：

```js
// 父级组件提供 'name'
export default {
  ...
  provide() {
  	return {
			name: 'zhang'  
		}
	}
}


// 子组件注入 'name'
export defalut {
  inject: ['name'],
  created () {
    console.log(this.name) // => "zhang"
  }
  // ...
}
```



##### `$parent`  /  `$children`

指定已创建的实例之父实例，在两者之间建立父子关系。子实例可以用 `this.$parent` 访问父实例，子实例被推入父实例的 `$children` 数组中。

通过全局的指令取父组件

```js
console.log(this.$parent)
```

![image-20190926184719197](https://tva1.sinaimg.cn/large/006y8mN6ly1g7d41t6czzj30u00ujaiq.jpg)


如果在一个组件内通过`this.$children`的话，拥有多个组件的话，取到的是一个组件的数组，如图：

![image-20190926184923320](https://tva1.sinaimg.cn/large/006y8mN6ly1g7d43vbl2cj30zu05eq4h.jpg)



但是一般官方提醒我们：

>  节制地使用 `$parent` 和 `$children` - 它们的主要目的是作为访问组件的应急方法。更推荐用 props 和 events 实现父子组件通信





##### `$attrs`/`$listeners`

- **类型**：`{ [key: string]: string }`

- **只读**

- **详细**：

  包含了父作用域中不作为 prop 被识别 (且获取) 的特性绑定 (`class` 和 `style` 除外)。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (`class` 和 `style` 除外)，并且可以通过 `v-bind="$attrs"` 传入内部组件——在创建高级别的组件时非常有用。

以上是官方原话。有点不好理解，就通过一个例子来理解。

父组件：

```html
<template>
  <div class="hello">
     <child :val1="val1" :val2="val2"></child>
  </div>
</template>

<script>
import Child from "./child";
export default {
  components: {
    Child
  },
  data() {
    return {
      val1: "msg one",
      val2: "msg two"
    };
  }
};
</script>
```

子组件：

```html
<template>
   <div>{{val1}} </div>
</template>
<script>
export default {
  name: "child",
  props: ["val1"]
};
</script>
```

父组件向子组件传递了 `val1` `val2` 两个参数， 但是在 子组件内部 `props` 只接收了一个参数 `val1`, 并没有接收 `val2`, 没有接收的多余的属性将会作为子组件根元素的属性节点。 `html` 形式就会显示为：

```html
<div val2='val2'>{{props val2}} </div>
```

发生上面的情况就是因为 `inheritAttrs` 选项默认是 `true` , 但是这和 `$attrs` 又有什么关系呢？

再来解读一下官网上面的解释:

>- **类型**：`boolean`
>
>- **默认值**：`true`
>
>- **详细**：
>
>  默认情况下父作用域的不被认作 props 的特性绑定 (attribute bindings) 将会“回退”且作为普通的 HTML 特性应用在子组件的根元素上。当撰写包裹一个目标元素或另一个组件的组件时，这可能不会总是符合预期行为。通过设置 `inheritAttrs` 到 `false`，这些默认行为将会被去掉。而通过 (同样是 2.4 新增的) 实例属性 `$attrs` 可以让这些特性生效，且可以通过 `v-bind` 显性的绑定到非根元素上。
>
>  注意：这个选项**不影响** `class` 和 `style` 绑定。

因为这个`inheritAttrs`是`ture`,且`val2`没有被`props`接收，所以会作为属性名绑到组件的根元素上。如果我们设置了

`inheritAttrs`是`false`,除了`class`和`style`，其他属性传到子组件的`$attrs`中，我们可以修改一下子组件：

```html
<template>
   <div>{{val1}} </div>
</template>
<script>
export default {
  name: "child",
  inheritAttrs: false,
  props: ["val1"],
  mounted(){
    console.log(this.$attrs)
  }
};
</script>
```

页面的打印结果就是：

```js
{val1: "msg one", val2: "msg two"}
```













**`$attrs`**：













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