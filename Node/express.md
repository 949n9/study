# express

Express 是一个后端框架，里面封装了很多node的函数。

## 安装

* 初始化一个项目

``` 
yarn init
```
*  安装express包
```
yarn add express
```

## 创建一个简单的express

```javascript
const express = require('express');
//引入express
const app = express();
//利用express创建一个应用

app.get('/',(req,res)=>{
    console.log(req.query);
    //查询字符串
    res.send('welcome to express')
    //
})
//如果服务器接收到get请求，则送出数据'welcome to express'
app.listen(3000)
//监听3000端口
```

## express-generator
### 安装express应用生成器
在全局去安装express应用生成器： express-generator
`yarn global add express-generator`
### 创建
`express 项目文件夹的名字 -e` 如果不加-e默认的是jade模板处理器，-e代表html
`express demo -e`
### 安装依赖包

`cd 项目文件夹的名字`
`npm install` 或者如果使用yarn 就用`yarn` `yarn install`

### 运行

`npm start`   `yarn start`

### 项目目录

`bin` :  `www` 启动一个web服务器
`node_modules` : 依赖包
`public` : 静态资源
`routes` : 路由文件夹
`view` :  xxx.ejs  项目的模板
`app.js` 项目的入口文件
`package.json`  ：依赖的配置文件