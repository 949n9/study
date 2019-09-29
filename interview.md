## cookie, sessionstorage, localstorage, session是什么？

* Session

  `Session`是在服务端保存的一个数据结构，用来跟踪用户的状态，这个数据可以保存在集群、数据库、文件中。

  ##### 基本概念

  Session是在无状态的HTTP协议下，服务端记录用户状态时用于标识具体用户的机制。它是在服务端保存的用来跟踪用户的状态的数据结构，可以保存在文件、数据库或者集群中。

  在浏览器关闭后这次的Session就消失了，下次打开就不再拥有这个Session。其实并不是Session消失了，而是Session ID变了，服务器端可能还是存着你上次的Session ID及其Session 信息，只是他们是无主状态，也许一段时间后会被删除。

  大多数的应用都是用Cookie来实现Session跟踪的，第一次创建Session的时候，服务端会在HTTP协议中告诉客户端，需要在Cookie里面记录一个SessionID，以后每次请求把这个会话ID发送到服务器。

* Cookie
	保存位置:浏览器端,存储内容只接受String类型。
	如果设置过期时间,保存在硬盘中,关闭浏览器后cookie数据仍然存在,直到过期事件结束才消失。
	如果未设置过期时间,保存在内存中,生命周期随浏览器的关系而结束,这种cookie简称为会话cookie。
	cookie与后台交互。
	
	#####应用场景:

	1)判断用户是否登录过网站,以便于下次登录时能够实现自动登录(或者记住密码);
	2)保存上、次查看的页;
	3)浏览次数。

	##### 缺点

	1)存储小:单个cookie保存的数据不能超过4kb;
	2)安全性低:别人可以分析存放在本地上的cookie并进行cookie欺骗;
	3)每一次请求都会带上cookie传给服务器,体验不好,浪费宽带;
	4)用户可以操作cookie,使功能受限。
	
	
	
* Storage
	webstorage是HTML5新出的标签，是本地存储的解决方案之一，有sessionStorage与localStorage两种。
	在HTML5中，新加入了一个localStorage特性，这个特性主要是用来作为本地存储来使用的，解决了cookie存储空间不足的问题(cookie中每条cookie的存储空间为4k)，localStorage中一般浏览器支持的是5M大小，这个在不同的浏览器中localStorage会有所不同。
	webstorage拥有封装好的方法，如setItem， getItem， removeItem，clear等。不像cookie那样需要程序猿手动封装。cookie的作用是与服务器进行交互，作为http规范的一部分存在为webstorage。而webstorage仅仅是为了在本地存储数据而生，它们都保存在客户端浏览器。
	
* localstorage
	
	**保存位置**:浏览器端,只能存储字符串类型。
	
	**生命周期**:
	
	localStorage的生命周期是永久的,关闭页面或者浏览器之后localStorage中的数据也不会消失。
	
	**应用场景**:长期登录、判断用户是否已登录、适合长期保存在本地的数据。
	
	**储存大小**:浏览器对localStorage的大小限制是5MB。
	
* sessionstorage

  **保存位置**:浏览器端,只能存储字符串类型。

  **生命周期**:

  仅在当前会话下有效,是在同源的窗口中始终存在的数据。只要这个浏览器没有关闭,即使刷新页面或者进入同源另一个页面,数据依然存在。浏览器窗口关闭后数据被销毁。

  **应用场景**:敏感账号一次性登录。

  **储存大小**:浏览器对sessionStorage的大小限制是5MB。

  

  
  
  
  
  ## 编程题
  
  
  
  
  
  ##大厂面试题
  
  ### 字节跳动
  
  1、讲讲ES6的箭头函数
  
  2、await和async 本质 
  
  3、类数组转化成数组的方法
  
  4、代码题，异步执行的顺序，宏观任务任务理解，promise、setTimeout等，让你写出输出的顺序 
  
  5、http缓存头部相关，Etag过程，cache-control的参数 
  
  6、代码题，思路是将一个嵌套的数组用深度遍历和广度遍历分别写出来
  
  
  
  
  
  在一个`span`标签中存在很多字，如果给span加一个border会如何？
  
  
  
  ![屏幕快照 2019-09-17 下午8.39.49](https://tva1.sinaimg.cn/large/006y8mN6ly1g72xsvfvwjj31ry0ksahq.jpg)
  
   每一行文字都会加上border，换行的时候不会有左右边框，只有开始和结束的时候会有左右边框。
  
  
  
  
  
  
  
  
  
  2 tcp的三次握手
  3 懒加载的节流和防抖，代码实现，并说明原理去区别还有使用场景
  4 css垂直居中，不定宽高和定宽高的2种实现办法
  5 写一个继承吧，组合继承，然后说出寄生组合继承相对于组合继承的优点
  6 ajax和后台对接传数据如何实现，中间需要注意什么细节，get还是post
  7 es6有学过吗？说一下你了解哪几个，我详细说了let var const的区别各种使用办法
  8 promise有用过吗？他是干啥的？你会在啥场景使用他，promise如何解决回凋地狱？如何和ajax配合？
  9 浏览器的渲染过程
  10 浏览器输入url发生什么？
  11 em和rem如何适配？有啥区别？还有其他适配的办法吗？
  12 前端性能如何优化列举几种吧
  13 你有canvas的经验，你可以大概说一下如何实现一个普通canvas的过程吗？可以写一下吗？
  14 还有啥问题问我吗？

大数相加问题

数组去重的问题

JS的数据类型

JS中基本数据类型和引用类型在内存上有什么区别？


tostring和valueof有什么区别
后来面试官问我，undefined==null吗？我说是的true，又问为什么。我说，==导致了两边对象进行了隐式数据类型转换，都变成了number类型的，这里两边都变成了0，所以返回true。
然后面试官就往深层次问了：[0]==0?
what???我知道[]==0是true，但是[0]==0??，我想[0]是数组对象，强制转换的时候会调用它的valueof方法，这里应该是返回0，两边应该相等吧…结果我并没有相信自己…就弱弱的说了句返回false吧。。。面试官后来都提示我了valueof返回的是什么？噗….
我说如果是对象的话会调用tostring然后转换成number进行比较，而数组对象则是valueof。。。
于是就问了我tostring和valueof的区别，很显然我答不上来。又gg一波。

2018.04.08更新：
valueof是返回最适合该对象类型的原始值，而tostring则是返回对象的字符串表示。

position的值
看来面试官心里有个底了，于是就开始问我css中position的取值有哪些。
嘿，这个
我还是能回答的。
“relative,static,absolute,sticky,fixed,initial,inherit,unset”。。。
面试官问我了，说absolute是相对哪个元素定位的？我依稀记得是相对不为static的元素定位的，但是不太确定。就说了相对父级元素中最近的relative定位。面试官好像不太满意我的回答…
然后又问了我unset有什么作用，OS:我没用过啊…，回答不知道。


谈一谈你对浮动的理解
ok，这个我还是会一点的。就说了浮动时高度塌陷，为了避免这个问题可以使用伪元素清除浮动，或者使用BFC容器。
好吧一提到清除浮动，面试官就问我了解过.clearfix这个类吗？

浏览器这块，了解过GC吗？
了解过一点，像JS的话就是利用标记清除法来回收内存的。然后就说了什么情况下添加标记，什么时候去除标记。

谈谈CDN加速
“你谈谈如何给对象添加标记”

谈谈HTTPS

！！ 字节跳动应届面试！！！！！太强。多看几遍！！理解！
https://www.nowcoder.com/discuss/177482

把一串数字转化成千分位分割的形式。
(12345123467.1234533527).toString().replace(/\B(?=(\d{3})+(?=\b))(?<=\b(?<!\.)\d*)/g, ',')

https://juejin.im/post/5abb5b01f265da237f1e5a92

事件循环机制
https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/7
https://juejin.im/post/59e85eebf265da430d571f89
在node环境下，process.nextTick的优先级高于Promise；在宏任务结束后会先执行微任务队列中的nextTickQueue部分，然后才会执行微任务中的Promise部分。



脏检查
https://www.jianshu.com/p/72db2f527068







## for循环里面加了异步函数的问题

```js
for( var i = 0; i < 5; i++){
    (function() { 
        setTimeout(()=>{
            console.log(i) 
        },1000*i) 
    })(i)  
}
//而console.log(i)中的i指向的是全局的i， var声明的变量是全局变量，所以在同步函数结束后，全局只有一个i = 5，产生闭包会去函数体外面取值。
//所以会打印出5个5

for( var i = 0; i < 5; i++){
    (function(i) { 
        setTimeout(()=>{
            console.log(i)  
        },1000*i)  
    })(i)  
}

//0 1 2 3 4 
//不同的是给function里面传值了，所以function(0）,function(1）,function(2）,function(3）...会被送到异步队列，
//由于不产生闭包，i 已经有值了，不用去全局取，所以会输入0 1 2 3 4

for( let i = 0; i < 5; i++){
    (function() {  
        setTimeout(()=>{
            console.log(i) 
        },1000*i)  
    })(i)  
}
//0 1 2 3 4
//let 是块级作用域，每次for循环运行一次都会产生一个块级作用域，所以可以看成有5个i 
```





## 什么是深克隆，浅克隆？实现深克隆（拷贝）

### 浅拷贝

![image-20190918162016245](https://tva1.sinaimg.cn/large/006y8mN6ly1g73qu98e9gj30u40d641z.jpg)



> 创建一个新对象，这个对象有着原始对象属性值的一份精确拷贝。如果属性是基本类型，拷贝的就是基本类型的值，如果属性是引用类型，拷贝的就是内存地址 ，所以如果其中一个对象改变了这个地址，就会影响到另一个对象。

### 深拷贝

网上比较流行的一个方案是：

```js
JSON.parse(JSON.stringify(object))
```

原理是利用JSON.parse把对象转换成JSON格式，然后再通过JSON.stringify把JSON转化成对象。

如果是安全的JSON格式的值，就不会出现问题，如果是不安全的JSON值，这个方法就会出现问题。

例如出现如下情况的：

* 对象中含有undefined,function,symbol
* 包循环引用（对象之间的互相引用）
* 其他类型，function，正则

如果出现上述情况的，都不可以用这个方法去拷贝。

```js
  function clone(target, map = new map()) {
    if (typeof target === 'object'){
        let cloneTarget = Array.isArray(target)? [] : {}
        if(map.get(target)){
            return map.get(target)
        }
        map.set(target, cloneTarget)
        for(const key in target){
            cloneTarget[key] = clone(target[key],map)
        }
        return cloneTarget
    } else {
        return target
    }
}

  //解决循环引用问题
  //但是对于symbol类型等其他类型还是不能使用
  //后续可以参考下文链接，或者使用lodash的深克隆


```

解决循环引用问题，我们可以额外开辟一个存储空间，来存储当前对象和拷贝对象的对应关系，当需要拷贝当前对象时，先去存储空间中找，有没有拷贝过这个对象，如果有的话直接返回，如果没有的话继续拷贝，这样就巧妙化解的循环引用的问题。

这个存储空间，需要可以存储`key-value`形式的数据，且`key`可以是一个引用类型，我们可以选择`Map`这种数据结构：

- 检查`map`中有无克隆过的对象
- 有 - 直接返回
- 没有 - 将当前对象作为`key`，克隆对象作为`value`进行存储
- 继续克隆


作者：ConardLi链接：https://juejin.im/post/5d6aa4f96fb9a06b112ad5b1









## 函数节流和函数防抖

#### 什么是函数节流和函数防抖？

函数防抖和函数节流都是为了防止函数被频繁调用从而造成性能的降低，用户体验差的问题。

下面这张图就可以防抖和节流，一根竖线代表函数执行了一次

![image-20190919110712373](https://tva1.sinaimg.cn/large/006y8mN6ly1g74netfvh7j30xs0lkta6.jpg)

#### 函数防抖

函数防抖: 任务频繁触发的情况下，只有任务触发的间隔超过指定间隔的时候，任务才会执行。

函数防抖的原理也非常地简单，通过闭包保存一个标记来保存 `setTimeout` 返回的值，每当用户输入的时候把前一个 `setTimeout` clear 掉，然后又创建一个新的 `setTimeout`，这样就能保证输入字符后的 `interval` 间隔内如果还有字符输入的话，就不会执行 `fn` 函数了。

```js
function debounce(fn, interval) {
    let timeout = null;
    return function () {
        clearTimeout(timeout);
        timeout = setTimeout(() => {
            fn.apply(this, arguments);
        }, interval);
    };
}
```

> - 代码解读
> 1. 第一次调用函数，创建一个定时器，在指定的时间间隔之后运行代码；
> 2. 当第二次调用该函数时，它会清除前一次的定时器并设置另一个；
> 3. 如果前一个定时器已经执行过了，这个操作就没有任何意义；
> 4. 然而，如果前一个定时器尚未执行，其实就是将其替换为一个新的定时器；
> 5. 目的是**只有在执行函数的请求停止了delay时间之后才执行**。




**应用：**

这里以用户注册时验证用户名是否被占用为例，如今很多网站为了提高用户体验，不会再输入框失去焦点的时候再去判断用户名是否被占用，而是在输入的时候就在判断这个用户名是否已被注册，当用户输入第一个字符的时候，就开始请求判断了，不仅对服务器的压力增大了，对用户体验也未必比原来的好。而理想的做法应该是这样的，当用户输入第一个字符后的一段时间内如果还有字符输入的话，那就暂时不去请求判断用户名是否被占用。在这里引入函数防抖就能很好地解决这个问题。


#### 函数节流

函数节流: 指定时间间隔内只会执行一次任务；

原理，函数的节流就是通过闭包保存一个标记（`canRun = true`），在函数的开头**判断**这个标记是否为 `true`，如果为 `true` 的话就继续执行函数，否则则 return 掉，判断完标记后立即把这个标记设为 `false`，然后把外部传入的函数的执行包在一个 `setTimeout` 中，最后在 `setTimeout` 执行完毕后再把标记设置为 `true`（这里很关键），表示可以执行下一次的循环了。当 `setTimeout` 还未执行的时候，`canRun` 这个标记始终为 `false`，在开头的判断中被 return 掉。

```js
function throttle(fn, interval) {
    let canRun = true;
    return function () {
        if (!canRun) return;
        canRun = false;
        setTimeout(() => {
            fn.apply(this, arguments);
            canRun = true;
        }, interval);
    };
}
// 第一种写法
function throttle(fn, threshold, scope) {
    let timer;
    let prev = Date.now();
    return function () {
        let context = scope || this, args = arguments;
        let now = Date.now();
        if (now - prev > threshold) {
            prev = now;
            fn.apply(context, args);
        }
    }
}
// 第二种时间戳写法


const t = throttle(console.log, 1000);

t("hello");
t("hello");
t("hello");

setTimeout(() => t("world"), 1100);

setTimeout(() => t("world"), 1200);

//测试用例
```



**应用：**

以判断页面是否滚动到底部为例，普通的做法就是监听 `window` 对象的 `scroll` 事件，然后再函数体中写入判断是否滚动到底部的逻辑，这样做的一个缺点就是比较消耗性能，因为当在滚动的时候，浏览器会无时不刻地在计算判断是否滚动到底部的逻辑，而在实际的场景中是不需要这么做的，在实际场景中可能是这样的：在滚动过程中，每隔一段时间在去计算这个判断逻辑。而函数节流所做的工作就是每隔一段时间去执行一次原本需要无时不刻地在执行的函数，所以在滚动事件中引入函数的节流是一个非常好的实践。



**场景**

| 类型     | 场景                                                         |
| -------- | ------------------------------------------------------------ |
| 函数防抖 | 1. 手机号、邮箱输入检测 2. 搜索框搜索输入（只需最后一次输入完后，再放松Ajax请求） 3. 窗口大小`resize`（只需窗口调整完成后，计算窗口大小，防止重复渲染） 4.滚动事件`scroll`（只需执行触发的最后一次滚动事件的处理程序） 5. 文本输入的验证（连续输入文字后发送 AJAX 请求进行验证，（停止输入后）验证一次就好 |
| 函数节流 | 1. `DOM`元素的拖拽功能实现（`mousemove`） 2. 射击游戏的 `mousedown`/`keydown`事件（单位时间只能发射一颗子弹） 3. 计算鼠标移动的距离（`mousemove`） 4. 搜索联想（`keyup`） 5. 滚动事件`scroll`，（只要页面滚动就会间隔一段时间判断一次） |

参考：https://justclear.github.io/throttle-and-debounce/

​				https://juejin.im/post/5c00f7fe51882516be2ee2fc



## 实现简单的curry

### 什么是curry

柯里化是一种将使用多个参数的一个函数转换成一系列使用一个参数的函数的技术。

举例来说，一个接收3个参数的普通函数，在进行柯里化后， 柯里化版本的函数接收一个参数并返回接收下一个参数的函数， 该函数返回一个接收第三个参数的函数。 最后一个函数在接收第三个参数后， 将之前接收到的三个参数应用于原普通函数中，并返回最终结果。

lodash的文档是这么写的

```js
var abc = function(a, b, c) { return [a, b, c];};
var curried = _.curry(abc);
curried(1)(2)(3); // => [1, 2, 3]
curried(1, 2)(3); // => [1, 2, 3]
curried(1, 2, 3); // => [1, 2, 3]
// Curried with placeholders.
curried(1)(_, 3)(2); // => [1, 2, 3]
```

### 实现简单的函数柯里化

```js
function curry(fn) {
  const ctx = this;
  function inner(...args) {
    if (args.length === fn.length) return fn.call(ctx, ...args);
    return (...innerArgs) => inner.call(ctx, ...args, ...innerArgs);
  }

  return inner;
}

// test
function test(a, b, c) {
  console.log(a, b, c);
}

const f1 = curry(test)(1);
const f2 = f1(2);
f2(3);


//es5
function curry (fn, arr = []) {
    return fn.length === arr.length ? fn.apply(null, arr) : function (...args) {
        return curry (fn, arr.concat(args))
    }
}


// 一行实现
const curry = (fn, arr = []) => fn.length === arr.length ? fn(...arr) : (...args) => curry(fn, [...arr, ...args]);
```



## 判断类型

### typeof

**适用场景**

`typeof`操作符可以准确判断一个变量是否为下面几个原始类型：

```js
typeof 'ConardLi'  // string
typeof 123  // number
typeof true  // boolean
typeof Symbol()  // symbol
typeof undefined  // undefined
复制代码
```

你还可以用它来判断函数类型：

```js
typeof function(){}  // function
复制代码
```

**不适用场景**

当你用`typeof`来判断引用类型时似乎显得有些乏力了：

```js
typeof [] // object
typeof {} // object
typeof new Date() // object
typeof /^\d*$/; // object
复制代码
```

除函数外所有的引用类型都会被判定为`object`。

另外`typeof null === 'object'`也会让人感到头痛，这是在`JavaScript`初版就流传下来的`bug`，后面由于修改会造成大量的兼容问题就一直没有被修复...

### instanceof

`instanceof`操作符可以帮助我们判断引用类型具体是什么类型的对象：

```
[] instanceof Array // true
new Date() instanceof Date // true
new RegExp() instanceof RegExp // true
复制代码
```

我们先来回顾下原型链的几条规则：

- 1.所有引用类型都具有对象特性，即可以自由扩展属性
- 2.所有引用类型都具有一个`__proto__`（隐式原型）属性，是一个普通对象
- 3.所有的函数都具有`prototype`（显式原型）属性，也是一个普通对象
- 4.所有引用类型`__proto__`值指向它构造函数的`prototype`
- 5.当试图得到一个对象的属性时，如果变量本身没有这个属性，则会去他的`__proto__`中去找

`[] instanceof Array`实际上是判断`Array.prototype`是否在`[]`的原型链上。

所以，使用`instanceof`来检测数据类型，不会很准确，这不是它设计的初衷：

```
[] instanceof Object // true
function(){}  instanceof Object // true
复制代码
```

另外，使用`instanceof`也不能检测基本数据类型，所以`instanceof`并不是一个很好的选择。

### toString

上面我们在拆箱操作中提到了`toString`函数，我们可以调用它实现从引用类型的转换。

> 每一个引用类型都有`toString`方法，默认情况下，`toString()`方法被每个`Object`对象继承。如果此方法在自定义对象中未被覆盖，`toString()`返回 `"[object type]"`，其中`type`是对象的类型。

```
const obj = {};
obj.toString() // [object Object]
复制代码
```

注意，上面提到了`如果此方法在自定义对象中未被覆盖`，`toString`才会达到预想的效果，事实上，大部分引用类型比如`Array、Date、RegExp`等都重写了`toString`方法。

我们可以直接调用`Object`原型上未被覆盖的`toString()`方法，使用`call`来改变`this`指向来达到我们想要的效果。

![image-20190919190010631](https://tva1.sinaimg.cn/large/006y8mN6ly1g751371c0gj30xi0u07wh.jpg)



## 用JS，HTML原生实现轮播图





## 实现一下EventEmitter

```js

class EventEmitter {
    constructor () {
        this._eventpool = {};
    }
    on (event, callback) {
        this._eventpool[event] ? this._eventpool[event].push(callback) : this._eventpool[event] = [callback]
    }
    emit (event, ...args) {
        this._eventpool[event] && this._eventpool[event].forEach(cb => cb(...args))
    }
    off (event) {
        if (this._eventpool[event]) {
            delete this._eventpool[event]
        }
    }
    once (event, callback) {
        this.on(event, (...args) => {
            callback(...args);
            this.off(event)
        })
    }
}
```



## 编程题

### 判断一个字符串是不是回文

#### 回文

定义： 类似于'noon'或者'level'这样，是一个正读和反读都一样的**字符串**

#### reverse

```js
function Palindromes(str) {
    let reg = /[\W_]/g; // \w 匹配所有字母和数字以及下划线； \W与之相反； [\W_] 表示匹配下划线或者所有非字母非数字中的任意一个；/g全局匹配
    let newStr = str.replace(reg, '').toLowerCase();
    let reverseStr = newStr.split('').reverse().join('')
    return reverseStr === newStr; // 与 newStr 对比
}
// str.split('').reverse().join('') === str
```

#### 递归

```js
function palin(str) {
    let reg = /[\W_]/g;
    let newStr = str.replace(reg, '').toLowerCase();
    let len = newStr.length;
    while(len >= 1) {
        console.log('--')
        if(newStr[0] != newStr[len - 1]) {
            // len = 0; // 为了终止 while 循环 否则会陷入死循环
            return false;
        } else {
            return palin(newStr.slice(1, len - 1)); 
        // 截掉收尾字符 再次比较收尾字符是否相等 
        // 直到字符串剩下一个字符（奇数项）或者 0 个字符（偶数项）
        }
    }
    return true;
}
```



## 百度面试题

in 的应用：

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/in

```js
    if(!("a" in window)){
        var a = 1
    }
    alert(a)// undefined
    
```

```
如果你只是将一个属性的值赋值为undefined，而没有删除它，则 in 运算仍然会返回true。
```









## 移动端300ms延迟，点击穿透

### 移动端300ms点击延迟由来：

**故事：**2007 年初。苹果公司在发布首款 iPhone 前夕，遇到一个问题：当时的网站都是为大屏幕设备所设计的。于是苹果的工程师们做了一些约定，应对 iPhone 这种小屏幕浏览桌面端站点的问题。

这当中最出名的，当属双击缩放(double tap to zoom)，这也是会有上述 300 毫秒延迟的主要原因。

双击缩放，顾名思义，即用手指在屏幕上快速点击两次，iOS 自带的 Safari 浏览器会将网页缩放至原始比例。 那么这和 300 毫秒延迟有什么联系呢？ 假定这么一个场景。用户在 iOS Safari 里边点击了一个链接。由于用户可以进行双击缩放或者双击滚动的操作，当用户一次点击屏幕之后，浏览器并不能立刻判断用户是确实要打开这个链接，还是想要进行双击操作。因此，iOS Safari 就等待 300 毫秒，以判断用户是否再次点击了屏幕。 鉴于iPhone的成功，其他移动浏览器都复制了 iPhone Safari 浏览器的多数约定，包括双击缩放，几乎现在所有的移动端浏览器都有这个功能。之前人们刚刚接触移动端的页面，在欣喜的时候往往不会care这个300ms的延时问题，可是如今touch端界面如雨后春笋，用户对体验的要求也更高，这300ms带来的卡顿慢慢变得让人难以接受。

也就是说，移动端浏览器会有一些默认的行为，比如双击缩放、双击滚动。这些行为，尤其是双击缩放，主要是为桌面网站在移动端的浏览体验设计的。而在用户对页面进行操作的时候，移动端浏览器会优先判断用户是否要触发默认的行为。

**重点：由于移动端会有双击缩放的这个操作，因此浏览器在click之后要等待300ms，看用户有没有下一次点击，也就是这次操作是不是双击。**

### 解决方法

#### 禁止缩放

```html
<meta name="viewport" content="user-scalable=no">
<meta name="viewport" content="initial-scale=1,maximum-scale=1">
```

表明这个页面是不可缩放的，那双击缩放的功能就没有意义了，此时浏览器可以禁用默认的双击缩放行为并且去掉300ms的点击延迟。

**缺点：**就是必须通过完全禁用缩放来达到去掉点击延迟的目的，然而完全禁用缩放并不是我们的初衷，我们只是想禁掉默认的双击缩放行为，这样就不用等待300ms来判断当前操作是否是双击。但是通常情况下，我们还是希望页面能通过双指缩放来进行缩放操作，比如放大一张图片，放大一段很小的文字。

#### 更改默认的视口宽度（通过响应式布局）

一开始，为了让桌面站点能在移动端浏览器正常显示，移动端浏览器默认的视口宽度并不等于设备浏览器视窗宽度，而是要比设备浏览器视窗宽度大，通常是980px。我们可以通过以下标签来设置视口宽度为设备宽度。

```
<meta name="viewport" content="width=device-width">
```

因为双击缩放主要是用来改善桌面站点在移动端浏览体验的，而随着响应式设计的普及，很多站点都已经对移动端坐过适配和优化了，这个时候就不需要双击缩放了，如果能够识别出一个网站是响应式的网站，那么移动端浏览器就可以自动禁掉默认的双击缩放行为并且去掉300ms的点击延迟。如果设置了上述`meta`标签，那浏览器就可以认为该网站已经对移动端做过了适配和优化，就无需双击缩放操作了。

这个方案相比方案一的好处在于，它没有完全禁用缩放，而只是禁用了浏览器默认的双击缩放行为，但用户仍然可以通过双指缩放操作来缩放页面。

#### fastClick

[FastClick](https://link.juejin.im/?target=https%3A%2F%2Flink.jianshu.com%3Ft%3Dhttps%3A%2F%2Fgithub.com%2Fftlabs%2Ffastclick)是 [FT Labs](https://link.juejin.im/?target=https%3A%2F%2Flink.jianshu.com%3Ft%3Dhttp%3A%2F%2Flabs.ft.com%2F)专门为解决移动端浏览器 300 毫秒点击延迟问题所开发的一个轻量级的库。FastClick的实现原理是在检测到touchend事件的时候，会通过DOM自定义事件立即出发模拟一个click事件，并把浏览器在300ms之后的click事件阻止掉。

原理的实现可以看看这里：https://zhuanlan.zhihu.com/p/28052894  

zepto是通过计算偏移量，fastClick是通过计算时间差，touchStart 和touchEnd的时间差

### 点击穿透问题

说完移动端点击300ms延迟的问题，还不得不提一下移动端点击穿透的问题。可能有人会想，既然click点击有300ms的延迟，那对于触摸屏，我们直接监听touchstart事件不就好了吗？
使用touchstart去代替click事件有两个不好的地方。
第一：touchstart是手指触摸屏幕就触发，有时候用户只是想滑动屏幕，却触发了touchstart事件，这不是我们想要的结果；
第二：使用touchstart事件在某些场景下可能会出现点击穿透的现象。

什么是**点击穿透**？
假如页面上有两个元素A和B。B元素在A元素之上。我们在B元素的touchstart事件上注册了一个回调函数，该回调函数的作用是隐藏B元素。我们发现，当我们点击B元素，B元素被隐藏了，随后，A元素触发了click事件。

这是因为在移动端浏览器，事件执行的顺序是touchstart > touchend > click。而click事件有300ms的延迟，当touchstart事件把B元素隐藏之后，隔了300ms，浏览器触发了click事件，但是此时B元素不见了，所以该事件被派发到了A元素身上。如果A元素是一个链接，那此时页面就会意外地跳转。

#### 注：浏览器事件触发的顺序

touchstart --> mouseover(有的浏览器没有实现) --> mousemove(一次) -->mousedown --> mouseup --> click -->touchend

Touch 事件中，常用的为 touchstart, touchmove, touchend 三种。除此之外还有touchcancel。 注意，原生事件中并没有tap事件。下面会解释tap事件怎么产生的。

![image-20190929200315852](https://tva1.sinaimg.cn/large/006y8mN6ly1g7gn3rse9kj31580u0k61.jpg)

解决方案：

1. 只用touch

   最简单的解决方案，完美解决点击穿透问题

   把页面内所有click全部换成touch事件（`touchstart`、’touchend’、’tap’），

   需要特别注意

   a标签，a标签的href也是click，需要去掉换成js控制的跳转，或者直接改成span + tap控制跳转。如果要求不高，不在乎滑走或者滑进来触发事件的话，span + touchend就可以了，毕竟tap需要引入第三方库

   

   不用a标签其实没什么，移动app开发不用考虑SEO，即便用了a标签，一般也会去掉所有默认样式，不如直接用span

2. 用FastClick


## 关于内存泄漏

https://juejin.im/post/5cb33660e51d456e811d2687#heading-10

## 阿里面试：

 怎么判断两个对象相等？

## 未整理



* CSS3的选择器

* 圆角 border-radius

* 多列布局 （multi-column layout）

* 阴影（Shadow） text-shadow

* css弹性盒子模型

* CSS3制作特效

  1) Transition 对象变换时的过渡效果

  *  transition-property 对象参与过渡的属性
  *  transition-duration 过渡的持续时间
  *  transition-timing-function 过渡的类型
  *  transition-delay 延迟过渡的时间



内嵌音频

```
<audio src="xxx.mp3" id="myAudio">Audio player not available.</audio>
```

媒体标签
 video
多媒体标签
 embed









9.如何理解闭包

1、定义： 嵌套在函数作用域中的函数，称为闭包函数。该作用域称为闭包环境。通过闭包函数可以访问闭包函数所在函数作用域中的形参与变量

2、表现形式： 使函数外部能够调用函数内部定义的变量

3、工作原理：
 利用了js中的垃圾回收机制，当一个函数被调用时，开辟空间，函数调用结束，释放空间，垃圾回收机制释放被调用结束的函数时，发现函数的变量正在被其他的函数调用，这些变量不会被释放，而且被永久驻留在内存，只有退出程序，才会被释放，或者是手工释放（=null） 

4、变量的作用域

要理解闭包，首先必须理解Javascript特殊的变量作用域。

变量的作用域分类：全局变量和局部变量。

特点：

1、函数内部可以读取函数外部的全局变量；在函数外部无法读取函数内的局部变量。

2、函数内部声明变量的时候，一定要使用var命令。如果不用的话，你实际上声明了一个全局变量！

 5、使用闭包的注意点

1）滥用闭包，会造成内存泄漏：由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。

2）会改变父函数内部变量的值。所以，如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值。



10.

由于浏览器同源策略，凡是发送请求URL的协议、域名、端口三者之间任意一与当前页面地址不同即为跨域。

（1）JSONP（jsonp跨域get请求） 
​     这种方式主要是通过动态创建一个script标签，浏览器对script的资源引用没有同源限制，同时资源加载到页面后会立即执行；（创建script标签向不同域提交http请求的不会被拒绝的方法，jsonp标签的src属性是没有跨域限制的）
​     实际项目中JSONP通常用来获取json格式数据，这时前后端通常约定一个参数callback，该参数的值，就是处理返回数据的函数名称；
缺点：这种方式无法发送post请求，另外要确定jsonp的请求是否失败并不容易，大多数框架的实现都是结合超时时间来判断。
（2）proxy 代理
这种方式首先将请求发送给后台服务器，通过服务器来发送请求，然后将请求的结果传递给前端；
需要注意的是如果你代理的是https协议的请求，那么你的proxy首先需要信任该证书或者忽略证书检查，否则你的请求无法成功。
（3）cors
当你使用XMLHttpRequest发送请求时，浏览器发现该请求不符合同源策略，会给该请求头origin，后台进行一系列处理，如果确定接受请求则在返回结果加入一个响应头Access-Control-Allow-Origin；浏览器判断该响应头中是否包含Origin的值，如果有则浏览器会处理响应，我们就可以拿到响应数据，如果不包含浏览器直接驳回，这时我们无法拿到响应数据； 













1.每个构造函数都有一个原型对象

每个原型对象都包含一个指向构造函数的指针

每个实例都包含一个指向原型对象的指针

查找方式是一层层向上查找直至顶层Object.prototype.

所以当一个实例对象去访问一个不存在的属性或方法时，就会自动去找构造函数所对应的原型对象





> **<script>标签的defer和async属性**











1.用flex实现水平居中

Html:

```html
<div class="father">
    <div class="son">
    </div>
</div>
```

css:

```css
*{
    margin: 0;
    padding: 0
}
.father{
    width: 400px;
    height: 400px;
    border: 1px dashed black;
    display: flex;/*父元素设置flex属性*/
    justify-content: center;/*水平主轴居中*/
    align-items: center;/*垂直交叉轴居中*/
}
.son{
    width: 100px;
    height: 50px;
    background: pink;
}
//父元素（容器）设置display属性为flex，并设置水平主轴上的元素居中，垂直交叉轴上的元素居中。
```



2.等高布局

Html：

```
<div class="parent">
        <div class="left">left</div>
        <div class="right">right</div>
</div>
```

Css：

使用flex：

```css
.parent{ display: flex; }
.left,.right{ flex: 1; }
```



4.检索字符

```js
let str = 'aerozhonghuan'
console.log(str.indexOf('s'))
//search,如果找到，返回值得索引，找不到为-1

let Rex = 's'
console.log(str.search(/s/g))
//search,如果找到，返回值得索引，找不到为-1

console.log(str.match(/s/g))
//match 如果找到，返回匹配的值组成的数组，找不到为null
```



5.获取屏幕点的方法

```js
        function getScreen(e) {
            var x = e.screenX - e.clientX;
            var y = e.screenY - e.clientY;
            alert("X坐标：" + x + ",Y坐标：" + y);
        }
```

