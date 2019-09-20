> 
>
> 导语
> ES5 提供了 Object.defineProperty 方法，该方法可以在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回这个对象。
>
> 该方法允许精确添加或修改对象的属性。通过赋值操作添加的普通属性是可枚举的，能够在属性枚举期间呈现出来（[`for...in`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...in) 或 [`Object.keys`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)[ ](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Object/keys)方法）， 这些属性的值可以被改变，也可以被[删除](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/delete)。这个方法允许修改默认的额外选项（或配置）。默认情况下，使用 `Object.defineProperty()` 添加的属性值是不可修改的。

## 语法

> defineProperty( obj ,  prop , descriptor )

### 参数

**obj**

要在其上定义属性的对象。

**prop**

要定义或修改的属性的名称。

**descriptor**


将被定义或修改的属性描述符。

### 属性描述符

对象里目前存在的属性描述符有两种主要形式：**数据描述符**和**存取描述符**。**数据描述符**是一个具有值的属性，该值可能是可写的，也可能不是可写的。**存取描述符**是由getter-setter函数对描述的属性。描述符必须是这两种形式之一；不能同时是两者。
即`value`和`writable` 这一组属性，与 `getter`和`setter`这一组属性不能同时存在。



**数据描述符和存取描述符均具有**以下可选键值(默认值是在使用Object.defineProperty()定义属性的情况下)：

- `configurable`

  当且仅当该属性的 configurable 为 true 时，该属性`描述符`才能够被改变，同时该属性也能从对应的对象上被删除。**默认为 false**。

- `enumerable`

  当且仅当该属性的`enumerable`为`true`时，该属性才能够出现在对象的枚举属性中（[`for...in`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...in) 或 [`Object.keys`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)[ ](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Object/keys)方法）。**默认为 false**。

**数据描述符同时具有以下可选键值**：

- `value`

  该属性对应的值。可以是任何有效的 JavaScript 值（数值，对象，函数等）。**默认为 undefined**。

- `writable`

  当且仅当该属性的`writable`为`true`时，`value`才能被[赋值运算符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Assignment_Operators)改变。**默认为 false**。

**存取描述符同时具有以下可选键值**：

- `get`

  一个给属性提供 getter 的方法，如果没有 getter 则为 `undefined`。当访问该属性时，该方法会被执行，方法执行时没有参数传入，但是会传入`this`对象（由于继承关系，这里的`this`并不一定是定义该属性的对象）。

  **默认为 undefined**。

- `set`

  一个给属性提供 setter 的方法，如果没有 setter 则为 `undefined`。当属性值修改时，触发执行该方法。该方法将接受唯一参数，即该属性新的参数值。

  **默认为 undefined**。

举个例子：

```js
let a = {}

Object.defineProperty(a,'name',{
    value: 'alex',
    writable: true,
    enumerable: true,
    configurable: true
})

console.log(a)
// {name : 'alex'}
```

```js
let b = {}

Object.defineProperty(b, 'name', {
    get: function () {
        console.log('get')
    },
    set: function () {
        console.log('set')
    },
    enumerable: true,
    configurable: true
})
```

## Setters 和 Getters

我们要使用存取描述符中的 get 和 set，这两个方法又被称为 getter 和 setter。由 getter 和 setter 定义的属性称做”存取器属性“。

当程序查询存取器属性的值时，JavaScript 调用 getter方法。这个方法的返回值就是属性存取表达式的值。当程序设置一个存取器属性的值时，JavaScript 调用 setter 方法，将赋值表达式右侧的值当做参数传入 setter。从某种意义上讲，这个方法负责“设置”属性值。可以忽略 setter 方法的返回值。


作者：冴羽链接：https://juejin.im/post/5be4f7cfe51d453339084530来源：掘金著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。