## 高阶函数


高阶函数是指参数是函数的，或者返回值是函数的函数

### before

在一个函数执行前先执行另一个函数。

用法：在`prototype.before`里面去封装

```javascript
const f1 = ()=>{
  console.log('f1函数执行了')
}
Function.prototype.before = function(beforefn){
  return ()=>{
    beforefn();
    this();
  }
}
const f2 = f1.before(()=>{
  console.log('f1的前置函数')
})
f2();
//f1的前置函数
//f1函数执行了
```

如果在`f1`中需要传递函数，就用`...args`来接收

```javascript
const f1 = (...args)=>{
		console.log('我是f1',...args)
}
Function.prototype.before = function(beforefn){
  return(...args)=>{
    beforefn();
    this(...args);
  }
}
const f2 = f1.before(()=>{
  console.log('f1的前置函数')
});
f2(1,2,3);
```

### after

在调用某个函数n次后，再触发另一个函数的执行

```js
const after = (times,fn)=>{
  return ()=>{
    if(--times === 0){
      fn();
    }
  }
}

const f1 = after(3,()=>{
  console.log('f1执行了')
})
f1()
f1()
f1()
//f1执行了
```

### 包括函数

在一个函数执行前和执行后放上多个函数

```js
const f1 = ()=>{
  console.log('f1执行了')
}
let wrappers = [
  {
   	before(){
      console.log('前置函数1')
    },
    after(){
      console.log('后置函数1')
    }
  },
  {
     before(){
      console.log('前置函数2')
    },
    after(){
      console.log('后置函数2')
    }
  }
]

const integrate = (fn,wrappers)=>{
  wrappers.forEach(item =>{
    	item.before()
  })
  fn()
  wrappers.forEach(item =>{
    	item.after()
  })
}
integrate(f1,wrappers)
//前置函数1
//前置函数2
//f1执行了
//后置函数1
//后置函数2
```



### all函数

指当两个任务都完成后再输出结果。有很多种写法。
* 采用计算次数来写
```javascript
const fs = require("fs")
let content = {};
const after = (times,fn)=>{
    return ()=>{
        if(--times === 0){
            fn()
        }
    }
}
let newAfter = after(2,()=>{
    console.log(content)
})
fs.readFile("name.txt","utf8",(err,data)=>{
    content['name'] = data
    newAfter()
})
fs.readFile("age.txt","utf8",(err,data)=>{
    content['age'] = data
    newAfter()
})
```

* 多个函数嵌套在一起
```js
 const fs = require("fs")
 let content = {};
 fs.readFile("name.txt","utf8",(err,data)=>{
     content['name'] = data
     fs.readFile("age.txt","utf8",(err,data)=>{
        content['age'] = data
         console.log(content) // { name: 'wangcai', age: '100' }
     })
 })
```
## 发布订阅

​      `    发布订阅模式不是一个死的机制,他是一种思想,一种写代码的形式;主要为了处理一对多的场景,应用于不同情况下的不同函数的调用,this很重要!`

​        发布订阅模式，基于一个主题/事件通道，希望接收通知的对象（称为subscriber）通过自定义事件订阅主题，被激活事件的对象（称为publisher）通过发布主题事件的方式被通知。

　　就和用户订阅微信公众号道理一样，一个公众号可以被多个用户同时订阅，当公众号有新增内容时候，只要发布就好了，用户就能接收到最新的内容。

​		 首先定义一个最简单的发布订阅模式，如下：

```javascript
let e = {
    arr: [],
    on(fn){
        this.arr.push(fn)
    },
    emit(){
        this.arr.forEach(fn => fn())
    }
}
e.on(()=>{
    console.log('function-one')
})
e.on(()=>{
    console.log('function-two')
})
e.emit()
//function-one 
//function-two
```

比较通用的Event订阅发布的模板：

```javascript
let event = {
    list: {},
    on(key, fn) {
    // 如果对象中没有对应的key值
    // 也就是说明没有订阅过
    // 那就给key创建个缓存列表
        if (!this.list[key]) {
            this.list[key] = [];
        }
        this.list[key].push(fn);
    },
    emit() {
        let key = [].shift.call(arguments),
            fns = this.list[key];

        if (!fns || fns.length === 0) {
            return false;
        }
        fns.forEach(fn => {
            fn.apply(this, arguments);
        });
    },
    remove(key, fn) {
        // 这回我们加入了取消订阅的方法
        let fns = this.list[key];
        // 如果缓存列表中没有函数，返回false
        if (!fns) return false;
        // 如果没有传对应函数的话
        // 就会将key值对应缓存列表中的函数都清空掉
        if (!fn) {
            fns && (fns.length = 0);
        } else {
            // 遍历缓存列表，看看传入的fn与哪个函数相同
            // 如果相同就直接从缓存列表中删掉即可
            fns.forEach((cb, i) => {
                if (cb === fn) {
                    fns.splice(i, 1);
                }
            });
        }
    }
};

function cat() {
    console.log('一起喵喵喵');
}
function dog() {
    console.log('一起旺旺旺');
}

event.on('pet', data => {
    console.log('接收数据');
    console.log(data);
});
event.on('pet', cat);
event.on('pet', dog);
// 取消dog方法的订阅
event.remove('pet', dog);
// 发布
event.emit('pet', ['二哈', '波斯猫']);
/*
    接收数据
    [ '二哈', '波斯猫' ]
    一起喵喵喵
*/

```

思路

* 创建一个对象(缓存列表)
* on方法用来把回调函数fn都加到缓存列表中
* emit方法取到arguments里第一个当做key，根据key值去执行对应缓存列表中的函数
* remove方法可以根据key值取消订阅
## 观察者模式

观察者模式是基于发布和订阅的。但是有区别，就是被观察者与观察者有联系。发布和订阅没有关系。

```javascript
class Subject {
    constructor(){
        this.arr = []
        this.state = 'happy'
    }
    attach(o){
        this.arr.push(o)
    }
    setState(newState){
        this.state = newState;
        this.arr.forEach( o => o.update(newState))
    }
}

class Observer{
    constructor(name){
        this.name = name;
    }
    update(newState){
        console.log(this.name, 'babys state',newState)
    }
}

let s = new Subject('baby')
let o1 = new Observer('me')
let o2 = new Observer('mywify')
s.attach(o1)
s.attach(o2)
s.setState('sad')
```



## Events

事件触发器，是node的核心

当 `EventEmitter` 对象触发一个事件时，所有绑定在该事件上的函数都会被同步地调用。 被调用的监听器返回的任何值都将会被忽略并丢弃。

例子，一个简单的 `EventEmitter` 实例，绑定了一个监听器。 `eventEmitter.on()` 用于注册监听器， `eventEmitter.emit()` 用于触发事件。

```javascript
let EventEmitter = require('events');
let e = new EventEmitter;
e.on('read',function () {
    console.log('read')
})

let f1 = function(){
    console.log('eat')
}

e.on('eat',f1)
e.emit('read')
e.emit('eat')
e.off('eat',f1)
e.emit('eat')
```

主要用法有三个

* on 用于订阅事件，基本用法就是`.on`
* emit 用于触发订阅事件，需要跟上触发的事件名
* off: off用于取消订阅的事件，用法如上代码所示，需要将回调函数独立放在外面，取消时需要调用`e.off('eat',f1)`

如何手写一个Events ，具体看上面观察模式。



## stream

### 定义

流的英文`stream`，流（Stream）是一个抽象的数据接口，`Node.js`中很多对象都实现了流，流是`EventEmitter`对象的一个实例，总之它是会冒数据（以 `Buffer`为单位），或者能够吸收数据的东西，它的本质就是让数据流动起来。 可能看一张图会更直观：

![16bdbb113be0341a](/Users/949n9/Markdown/Node.resources/16bdbb113be0341a.png)

注意：`stream`不是node.js独有的概念，而是一个操作系统最基本的操作方式，只不过node.js有API支持这种操作方式。linux命令的|就是`stream`。

### 为什么要学习stream

#### 视频播放例子

小伙伴们肯定都在线看过电影，对比定义中的图-`水桶管道流转图`，`source`就是服务器端的视频，`dest`就是你自己的播放器(或者浏览器中的flash和h5 video)。大家想一下，看电影的方式就如同上面的图管道换水一样，一点点从服务端将视频流动到本地播放器，一边流动一边播放，最后流动完了也就播放完了。

说明：视频播放的这个例子，如果我们不使用管道和流动的方式，直接先从服务端加载完视频文件，然后再播放。会造成很多问题

1. 因内存占有太多而导致系统卡顿或者崩溃
2. 因为我们的网速 内存 cpu运算速度都是有限的，而且还要有多个程序共享使用，一个视频文件加载完可能有几个g那么大。

#### 读取大文件data的例子

**有一个这样的需求，想要读取大文件data的例子**

使用文件读取

```javascript
const http = require('http');
const fs = require('fs');
const path = require('path');

const server = http.createServer(function (req, res) {
    const fileName = path.resolve(__dirname, 'data.txt');
    fs.readFile(fileName, function (err, data) {
        res.end(data);
    });
});
server.listen(8000);

```

使用文件读取这段代码语法上并没有什么问题，但是如果data.txt文件非常大的话，到了几百M，在响应大量用户并发请求的时候，程序可能会消耗大量的内存，这样可能造成用户连接缓慢的问题。而且并发请求过大的话，服务器内存开销也会很大。这时候我们来看一下用`stream`实现。

```javascript
const http = require('http');
const fs = require('fs');
const path = require('path');

const server = http.createServer(function (req, res) {
    const fileName = path.resolve(__dirname, 'data.txt');
    let stream = fs.createReadStream(fileName);  // 这一行有改动
    stream.pipe(res); // 这一行有改动
});
server.listen(8000);

```

使用stream就可以不需要把文件全部读取了再返回，而是一边读取一边返回，数据通过管道流动给客户端，真的减轻了服务器的压力。

### 基本使用

```javascript
const fs = require('fs');
const path = require('path');

let rs = fs.createReadStream(path.join(__dirname,'name.txt'),{
    flags: 'r',
    highWaterMark: 5
})

let arr = []
rs.on("data",function(chunk){
    // chunk表示每一滴水
    arr.push(chunk)
})
rs.on("end",function(){ // 30--->48--->0  0,1,2,3   4,5
    // console.log(arr)  // [ <Buffer 30 31 32 33>, <Buffer 34 35> ]
    // console.log( Buffer.concat(arr)) // <Buffer 30 31 32 33 34 35 36>
    console.log(Buffer.concat(arr).toString()) // 0123456
})
```