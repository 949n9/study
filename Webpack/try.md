##Webpack4.0 学习之路

**安装**

```js
npm install webpack webpack-cli -g
//webpack 和webpack-cil 都需要安装
```

> 不推荐全局安装 webpack。这会将你项目中的 webpack 锁定到指定版本，并且在使用不同的 webpack 版本的项目中，可能会导致构建失败。-- 官方原话

那就安装在本地吧

```
npm install --save-dev webpack webpack-cli
```



**新建项目：**

```
npm init 
// 自选
npm init --y
//跳过自选

npm install webpack webpack-cli --save-dev
//官方强烈推荐 本地安装，所以每次都按一遍吧  - -
```



然后新建如下目录结构:



```
  webpack-demo
  |- package.json
+ |- index.html
+ |- /src
+   |- index.js
```





打开package.json文件

修改：

```
’private‘: true
//如果private为true，npm会拒绝发布。这可以防止私有repositories不小心被发布出去。
```

```
"script":{
		"test": ...
		"dev": "webpack --mode deveploment"
		"bulid": "webpack --mode production"
}
//配置指令，这里是配置打包指令
```

