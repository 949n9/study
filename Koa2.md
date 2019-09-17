#简介

Koa 是一个新的 web 框架，由 Express 幕后的原班人马打造， 致力于成为 web 应用和 API 开发领域中的一个更小、更富有表现力、更健壮的基石。 通过利用 async 函数，Koa 帮你丢弃回调函数，并有力地增强错误处理。 Koa 并没有捆绑任何中间件， 而是提供了一套优雅的方法，帮助您快速而愉快地编写服务端应用程序。



# 安装

`yarn add koa`
`npm install koa`

# 使用

## 简单的hello world 应用

* 新建hello.js在根目录
```javascript
const Koa = require('koa');
const app = new Koa();

app.use( async (ctx,next) => {
    ctx.body = 'hello Koa'
});

app.listen(3000)

```
如果我们在浏览器输入不同的路径匹配到的还是同一个界面，正常输入不同的url应该匹配到不同的界面，那要怎么写，可以如下：
```javascript
app.use(async (ctx, next) => {
    if (ctx.request.path === '/') {
        ctx.response.body = 'index page';
    } else {
        await next();
    }
});

app.use(async (ctx, next) => {
    if (ctx.request.path === '/test') {
        ctx.response.body = 'TEST page';
    } else {
        await next();
    }
});

app.use(async (ctx, next) => {
    if (ctx.request.path === '/error') {
        ctx.response.body = 'ERROR page';
    } else {
        await next();
    }
});
```
但是如果每次都要写`ctx.request.path`就会显得比较麻烦。所以就需要引进一个中间件。`koa-router`，使用了这个中间件就可以像express那样`router.get()`或者`router.post()`
# 常用的中间件
## koa-router
首先先安装`yarn add koa-router`
为了处理URL，我们需要引入`koa-router`这个middleware，让它负责处理URL映射。
使用实例如下：
```javascript
const Koa = require('koa');
const app = new Koa();
const router = require('koa-router')();
//应用后面跟一个（）相当于直接调用，相当于
// const fn = require('koa-router')
// const router = fn()

app.use(async (ctx, next) => {
    console.log(`Process ${ctx.request.method} ${ctx.request.url}...`);
    await next();
});

// add url-route:
router.get('/hello/:name', async (ctx, next) => {
    var name = ctx.params.name;
    ctx.response.body = `<h1>Hello, ${name}!</h1>`;
});

router.get('/', async (ctx, next) => {
    ctx.response.body = '<h1>Index</h1>';
});

// add router middleware:
app.use(router.routes());

app.listen(3000)

```
我们可以通过 `router.get('/path',async fn)`来注册一个get请求
如果需要跟上查询字符串可以写成 `router.get('/path:name')`
解析浏览器的地址之后可以通过`ctx.params.name`拿到具体的数值
如果是多个的话，通过`ctx.params`来取值
```javascipt
    router.get('/hello/:name&:id', async (ctx, next) => {
    var name = ctx.params;
    console.log(name)
    ctx.response.body = `<h1>Hello, ${name}!</h1>`;
});
//浏览器：http://localhost:3000/hello/xiaomi&1312312
//{ name: 'xiaomi', id: '1312312' }
```
处理post请求
用router.get('/path', async fn)处理的是get请求。如果要处理post请求，可以用router.post('/path', async fn)。
用post请求处理URL时，我们会遇到一个问题：post请求通常会发送一个表单，或者JSON，它作为request的body发送，但无论是Node.js提供的原始request对象，还是koa提供的request对象，都不提供解析request的body的功能。
所以，我们又需要引入另一个middleware来解析原始request请求，然后，把解析后的参数，绑定到ctx.request.body中。
koa-bodyparser就是用来干这个活的。
## koa-bodyparser
首先安装`yarn add koa-bodyparser`
引入`const bodyparser = require('koa-bodyparser')`
调用`app.use(bodyParser())`
然后就可以注册post请求了，下面的例子要求用户名是koa，密码是12345才可以登录，否则返回重新登录
```javascript
const Koa = require('koa');
const app = new Koa();
const router = require('koa-router')();
const bodyParser = require('koa-bodyparser')

app.use(bodyParser());

router.get('/',async (ctx,next)=>{
    ctx.body = `<h1>Login</h1>
                <form action="/login" method="post">
                <p>Name: <input name="name" value=""></p>
                <p>Password: <input type="password" name="password"></p>
                <p><input type="submit" value="Submit"></p>
                </form>`
    await next()
});

router.post('/login', async (ctx, next) => {
    let name = ctx.request.body.name || ''
    let password = ctx.request.body.password || ''
    console.log(`login name: ${name}, passwrod: ${password}`)
    if (name === 'koa' && password === '12345'){
        ctx.body = `<div>
                     <h1>Welcome, ${name}</h1>
                    </div>
                    `
    }else {
        ctx.body = `<div>
                     <h1>Login Failed!</h1>
                     <a href="/">Try again?</a>
                    </div>
        `
    }
    await next()
})

// add router middleware:
app.use(router.routes());
// listen post 3000
app.listen(3000)

```


## koa-views

koa-views用来加载html模板文件列入ejs或者pug
安装 `yarn add koa-views`
后面如果没有办法解析ejs模块还需要安装ejs的包`yarn add ejs`
`const views = require('koa-views')`
`app.use(views(__dirname + '/views', {
  extension: 'ejs'
}))`
代码示例：
```javascript
const Koa = require('koa');
const path = require('path');
const views = require('koa-views');

const app = new Koa()

app.use(views(path.join(__dirname,'/views'),{
    extension: 'ejs'
}))

app.use(async (ctx, next) => {
    await ctx.render('index.ejs',{title: 'koa'})//把title的值传入index
    await next()
})

app.listen(3001)
// 把数据渲染到模板中，然后把模板返回浏览器
// koa-views

```
index.ejs  如下 ：
```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
<div>
    <h1><%= title%></h1>
</div>
</body>
</html>
```



## koa-static
koa-static用来托管静态资源，如一些css，图片，js
使用：

```js
const Koa = require("koa")
// koa-static是用来托管静态资源
const serve = require('koa-static');
var app = new Koa();
app.use(serve(__dirname + '/public'));

app.listen(3030)
```

然后pubilc下面有个入口文件index.html,编写需要显示的html，也可以引入相应的样式，js。目录结构如下
![image-20190822204631957](https://tva1.sinaimg.cn/large/006y8mN6ly1g72xqt7u34j30ck0660sx.jpg)

以下是相关页面代码，样式和js省略...

```js
//app.js
const Koa = require("koa")
// koa-static是用来托管静态资源
const serve = require('koa-static');
var app = new Koa();
app.use(serve(__dirname + '/public'));

app.listen(3030)
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <link rel="stylesheet" href="./css/index.css">
    <script src="./js/index.js"></script>
</head>
<body>
<h1>这是一个静态资源...</h1>
</body>
</html>
```

## koa-multer

koa框架中用来上传图片的模块。

### 使用

`yarn add koa-multer`或`npm install koa-multer`
在需要使用的路由上写如下代码：
```javascript
const multer = require("koa-multer")

// 上传文件的相关配置
var storage = multer.diskStorage({
    //文件保存路径
    destination: function (req, file, cb) {
        cb(null, 'public/uploads/')  // 上传的文件 保存在什么地方
    },
    //修改文件名称
    filename: function (req, file, cb) {
        var fileFormat = (file.originalname).split(".");  //以点分割成数组，数组的最后一项就是后缀名
        cb(null, Date.now() + "." + fileFormat[fileFormat.length - 1]);
    }
})
//加载配置
var upload = multer({ storage: storage });
```
配置完再同一个路由上，写请求。
```javascript
router.post("/doAdd", upload.single('pic'), async (ctx)=>{
    ctx.body = {
        filename:ctx.req.file.filename,
        body:ctx.req.body
    }
})
```
需要注意的一点是在前端页面的`form`标签上需要有`enctype="multipart/form-data"`属性然后`input`的类型是`file`，如下：
```HTML
<form action="/admin/article/doAdd" method="POST" role="form" enctype="multipart/form-data">
     <div class="form-group">
       <label>上传图片:</label>
             <div class="col-sm-10">
                 <input type="file" name="pic" id="pic">
             </div>
     </div>
</form>
//input标签中的name需要和路由中的upload.single('pic')需要一样。
```



## koa2-ueditor

koa的富文本编辑器

本质就是[ueditor](http://fex.baidu.com/ueditor/)

### 使用

`yarn add koa2-ueditor`
然后到git上搜koa2-ueditor,下载，提取public下的ueditor到你的静态资源路径下
* 首先配置路由
```javascript
const ueditor = require("koa2-ueditor")
//引入模块
router.all('/editor/controller', ueditor(['public', {
    "imageAllowFiles": [".png", ".jpg", ".jpeg"],
    "imagePathFormat": "/upload/ueditor/image/{yyyy}{mm}{dd}/{filename}"  // 保存为原文件名
}]))
//配置中间件，需要注意的是/editor/controller 要和ueditor文件目录下的config文件相对应
```
* 配置config文件

  ![image-20190829181531492](https://tva1.sinaimg.cn/large/006y8mN6ly1g72xqygw0cj30mi03qgls.jpg)
由于本项目的接口是在admin下的，所以这里应该配置`"/admin/editor/controller"`

*在html文件创建富文本编辑器
```HTML
    <script id="container" name="content" type="text/plain">
        这里写你的初始化内容
    </script>
    //创建富文本编辑器，直接插入到富文本编辑器需要使用的位置
    <script type="text/javascript">
        var ue = UE.getEditor('container');
    </script>
    //实例化富文本编辑器
```
* 引入配置文件
```HTML
    <script type="text/javascript" charset="utf-8" src="/ueditor/ueditor.config.js"></script>
    <script type="text/javascript" charset="utf-8" src="/ueditor/ueditor.all.js"> </script>
    <script type="text/javascript" charset="utf-8" src="/ueditor/lang/zh-cn/zh-cn.js"></script>
    //在需要使用富文本的页面引入，这里我们的项目别的地方需要到这个配置文件，就在公共部分映入配置
```
* 具体配置富文本编辑器
在ueditor下有一个文件config，里面有说明配置的详细设置。

### 存后台传来的数据
```html
<script>
    let ue = UE.getEditor("editor")
    // 往编辑器中放内容
    ue.addListener("ready",function(){
        ue.setContent(`{{@list.content}}`)
    })
</script>
//{{@list.content}}是后台传过来的数据
//固定用法
```

## 需要注意的事项（晚点分类）

### ctx.req 和ctx.request的区别

如果使用的是get请求，那么使用ctx.req 和ctx.request的效果是一样的。里面存放的数据是一样的

但是如果你使用的是post请求，那么ctx.req是代表原生的node对象，对象不会经过中间件的解析。

而ctx.request则是经过中间件处理后的，所以我们一般最好Post请求中使用ctx.request