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





在 webpack 4 中，可以无须任何配置使用，然而大多数项目会需要很复杂的设置，这就是为什么 webpack 仍然要支持 [配置文件](https://www.webpackjs.com/concepts/configuration)。这比在终端(terminal)中手动输入大量命令要高效的多，所以让我们创建一个取代以上使用 CLI 选项方式的配置文件：

```js
  webpack-demo
  |- package.json
+ |- webpack.config.js
  |- /dist
    |- index.html
  |- /src
    |- index.js
```



**webpack.config.js**：

```
const path = require('path')
//引入路径模块

module.export = {
	entry:'./src/index.js',
	output:{
		filename: ''
		path: path.resolve(__dirname,'dist')
	}
}


如果我们的入口文件有多个，我们可以这样配置
module.export = {
	entry: {
			index: './src/index.js',
			about: './src/about.js'
	},
	//这里的键名就打包后的名字，对应下面的name
	output:{
		filename: '[name].[contenthash:7].js'
		//入口文件，contenthash是根据内容生成一段Hash，：7代表生成的hash是7位的
		chunkFilename: '[id].[chunkhash:7].js'
		//chunkFile 就是我们在入口文件里面动态加载的css或者js文件，属于非入口文件
		path： path.resolve(__dirname,'dist')
	},
	mode: production
	//指的是生产环境，就是会对源码进行压缩。也可以是development 不压缩
}
```

## plugins

### clean-webpack-plugin

因为每一次打包，如果内容变了，都会生成新的打包文件， 手动删除以前的打包文件会显得很麻烦，通常，在每次构建前清理 `/dist` 文件夹，是比较推荐的做法，因此用到了`clean-webpack-plugin`

> 需要注意的一点是，由于clean-webpack-plugin已经升级了，所以在官网的用法可能会报错，如果是最新的版本就需要结构出
>
> `CleanWebpackPlugin`，用法参考下面。

```js
  const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');
  const { CleanWebpackPlugin } = require('clean-webpack-plugin');

  module.exports = {
    entry: {
      app: './src/index.js',
      print: './src/print.js'
    },
    plugins: [
      new CleanWebpackPlugin(),
    ],
    output: {
      filename: '[name].bundle.js',
      path: path.resolve(__dirname, 'dist')
    }
  };
```

### webpack-devserve

用来热更新

```
devServer:{
	contentBase: './dist',
	//配置热更新的文件夹
	host：‘localhost’,
	port: 8080
	compress: true 
	//是否
}
```



### HtmlwebpackPlugin

用来自动生成html文件





https://juejin.im/post/5cea1e1ae51d4510664d1652