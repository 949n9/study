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

**`$attrs`**：

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



> 需要注意的两点:
>
> 作为中间传递数据的组件，必须要把 inheritAttrs 这个选项设置为 false 才能正确获取 $attrs 数据。
> v-bind 在平常使用时都是使用 v-bind:src=xx 或者 :src=xx 这种形式， 这里使用 v-bind=xx 是指绑定一个包含键值对的对象到组件。



**`$listeners`**：

- **类型**：`{ [key: string]: Function | Array<Function> }`

- **只读**

- **详细**：

  包含了父作用域中的 (不含 `.native` 修饰器的) `v-on` 事件监听器。它可以通过 `v-on="$listeners"` 传入内部组件——在创建更高层次的组件时非常有用。



与`$attrs`的用法和作用很类似，但是`$attrs`是用来传递数据的，`$listeners`是用来传递方法。

同 `$attrs` 的使用相似, 中间组件对于父级传递的事件并没有使用，也是作为一个过度，使用 `v-on='this.$listeners'` 将从父级接受来的事件再次向下传递，直到传递给最后一级组件， 最后一级的组件就能够使用` this.$listeners `来调用父级的事件，从而改变父级绑定在子组建上面的属性值。

下面来个例子：

孙组件：

```html
<template>
  <div>
      {{second}}{{third}}
      <button @click='handleFunc1'>fuc1 button</button>
      <button @click='handleFunc2'>fuc2 button</button>
  </div>
</template>

<script>
export default {
  name: "next",
  props: ["second", "third"], 
  methods: {
      handleFunc1() {
          this.$listeners.func1('props changed second')
      },
      handleFunc2() {
          this.$listeners.func2('props changed third')
      }
  }
};
</script>
```

子组件：

```html
<template>
  <next v-bind="$attrs" v-on='$listeners'></next>
</template>

<script>
import Next from './next'
export default {
  name: "child",
  components: {Next},
  inheritAttrs: false
};
</script>
```

父组件：

```html
<template>
 <child 
    :second='second' 
    :third='third'
    @func1='Func1Click' 
    @func2='Func2Click' 
/>
</template>

<script>
import Child from './child'
export default {
  components: {Child},
  data(){
      return {
          second: 'props second',
          third: 'props third'
      }
  },
  methods:{
      Func1Click(val) {
         this.second = val
      },
      Func2Click(val) {
         this.third = val
      }
  }
};
</script>
```



### 动态组件&异步组件



#### 动态组件

有时候，例如我们需要在不同组件间进行动态切换。例如一个标签选项卡。

这个时候我们可以用的动态组件。 基本用法如下:

* 使用Vue的 `<component>`元素
* 使用`v-bind:is`特性

```html
<!-- 组件会在 `currentTabComponent` 改变时改变 -->
<component v-bind:is="currentTabComponent"></component>
```

一个选项卡的例子：

```html
<script src="https://unpkg.com/vue"></script>

<div id="dynamic-component-demo" class="demo">
  <button
    v-for="tab in tabs"
    v-bind:key="tab"
    v-bind:class="['tab-button', { active: currentTab === tab }]"
    v-on:click="currentTab = tab"
  >{{ tab }}</button>

  <component
    v-bind:is="currentTabComponent"
    class="tab"
  ></component>
</div>
```

JS：

```js
Vue.component('tab-home', { 
	template: '<div>Home component</div>' 
})
Vue.component('tab-posts', { 
	template: '<div>Posts component</div>' 
})
Vue.component('tab-archive', { 
	template: '<div>Archive component</div>' 
})

new Vue({
  el: '#dynamic-component-demo',
  data: {
    currentTab: 'Home',
    tabs: ['Home', 'Posts', 'Archive']
  },
  computed: {
    currentTabComponent: function () {
      return 'tab-' + this.currentTab.toLowerCase()
    }
  }
})
```

在这个例子中`v-bind:is `绑定的是一个计算属性，基本思路就是当点击按钮的时候把`currentTab = tab`，然后再通过计算属性把名字还原 `tab-posts`这样，完成动态组件的切换。 



在上述示例中，`currentTabComponent` 可以包括

- 已注册组件的名字

  上述例子就是已经注册组件的名字

- 一个组件的选项对象

  举个例子：

  ```js
  var tabs = [
    {
      name: 'Home', 
      component: { 
        template: '<div>Home component</div>' 
      }
    },
    {
      name: 'Posts',
      component: {
        template: '<div>Posts component</div>'
      }
    },
    {
      name: 'Archive',
      component: {
        template: '<div>Archive component</div>',
      }
    }
  ]
  
  new Vue({
    el: '#dynamic-component-demo',
    data: {
    	tabs: tabs,
      currentTab: tabs[0]
    }
  })
  ```

  

#### 在组件上使用 `Keep-alive`

> keep-alive是一个抽象组件：它自身不会渲染一个 DOM 元素，也不会出现在父组件链中；使用keep-alive包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们。

当在这些组件之间切换的时候，你有时会想保持这些组件的状态，以避免反复重渲染导致的性能问题。

[https://cn.vuejs.org/v2/guide/components-dynamic-async.html#%E5%9C%A8%E5%8A%A8%E6%80%81%E7%BB%84%E4%BB%B6%E4%B8%8A%E4%BD%BF%E7%94%A8-keep-alive](https://cn.vuejs.org/v2/guide/components-dynamic-async.html#在动态组件上使用-keep-alive)

官方案例解释

##### 基本用法

```html
<!-- 失活的组件将会被缓存！-->
<keep-alive>
  <component v-bind:is="currentTabComponent"></component>
</keep-alive>
```

当组件在 `<keep-alive>` 内被切换，它的 `activated` 和 `deactivated` 这两个生命周期钩子函数将会被对应执行。

> 在 2.2.0 及其更高版本中，`activated` 和 `deactivated` 将会在 `<keep-alive>` 树内的所有嵌套组件中触发。

##### `keep-alive`的生命周期



1. activated： 页面第一次进入的时候，钩子触发的顺序是created->mounted->activated
2. deactivated:  页面退出的时候会触发deactivated，当再次前进或者后退的时候只触发activated



##### 运用场景

用户在某个列表页面选择筛选条件过滤出一份数据列表，由列表页面进入数据详情页面，再返回该列表页面，我们希望：列表页面可以保留用户的筛选（或选中）状态。keep-alive就是用来解决这种场景。当然keep-alive不仅仅是能够保存页面/组件的状态这么简单，它还可以避免组件反复创建和渲染，有效提升系统性能。
总的来说，keep-alive用于保存组件的渲染状态。


##### props



**`include` and `exclude`**

* `include`定义缓存白名单，`keep-alive`会缓存命中的组件
* `exclude`定义缓存黑名单，被命中的组件将不会被缓存





二者都可以用逗号分隔字符串、正则表达式或一个数组来表示,例如我们有`a`,`b`两个组件：

```html
<!-- 逗号分隔字符串 -->
<keep-alive include="a,b">
  <component :is="view"></component>
</keep-alive>

<!-- 正则表达式 (使用 `v-bind`) -->
<keep-alive :include="/a|b/">
  <component :is="view"></component>
</keep-alive>

<!-- 数组 (使用 `v-bind`) -->
<keep-alive :include="['a', 'b']">
  <component :is="view"></component>
</keep-alive>


//这里是设置白名单
```

匹配首先检查组件自身的 `name` 选项，如果 `name` 选项不可用，则匹配它的局部注册名称 (父组件 `components` 选项的键值)。匿名组件不能被匹配。




**`max`**

`max`定义缓存组件上限，超出上限使用[LRU的策略](https://link.juejin.im/?target=https%3A%2F%2Fbaike.baidu.com%2Fitem%2FLRU)置换缓存数据。

也就是说一旦这个数字达到了，在新实例被创建之前，已缓存组件中最久没有被访问的实例会被销毁掉。

```html
<keep-alive :max="10">
  <component :is="view"></component>
</keep-alive>
```

参考 链接：https://juejin.im/post/5cce49036fb9a031eb58a8f9





##### 原理

Vue.js内部将DOM节点抽象成了一个个的[VNode节点](https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2Fanswershuto%2FlearnVue%2Fblob%2Fmaster%2Fdocs%2FVNode%E8%8A%82%E7%82%B9.MarkDown)，keep-alive组件的缓存也是基于VNode节点的而不是直接存储DOM结构。它将满足条件（pruneCache与pruneCache）的组件在cache对象中缓存起来，在需要重新渲染的时候再将vnode节点从cache对象中取出并渲染。

附上链接

链接：https://juejin.im/post/5cce49036fb9a031eb58a8f9



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