# Node.js

## 应用

* 作为前端的工具 脚手架 npm
* 作为服务端的runtime js
* 中间层： 跨域问题，过滤数据
* ssr：服务端渲染

[TOC]





## process

`process` 

| api | 说明 |
| --- | --- |
| process.cwd | 运行的目录 |
| process.env | 运行的环境 |
| process.nextTick  | 微任务 |

## Buffer
`Buffer` 类的实例类似于从 0 到 255 之间的整数数组（其他整数会通过 ＆ 255 操作强制转换到此范围），但对应于 V8 堆外部的固定大小的原始内存分配。`Buffer` 的大小在创建时确定，且无法更改。

Buffer 类在全局作用域中，因此无需使用 `require('buffer').Buffer`
### Buffer.from

* `Buffer.from(array)` 返回一个新的 `Buffer`，其中包含提供的八位字节数组的副本。
* `Buffer.from(buffer)` 返回一个新的 `Buffer`，其中包含给定 `Buffer` 的内容的副本。
* `Buffer.from(string[, encoding])` 返回一个新的 `Buffer`，其中包含提供的字符串的副本。
```js
const a1 = Buffer.from('10');
console.log(a1);//<Buffer 31 30>
const a2 = Buffer.from('10', 'base64' );
console.log(a2);//<Buffer d7>
const a3 = Buffer.from(a2);
console.log(a3);//<Buffer d7>

// 创建一个包含字符串 'buffer' 的 UTF-8 字节的新 Buffer。
const buf = Buffer.from([2, 2, 2, 2, 2, 2]);
console.log(buf);//<Buffer 02 02 02 02 02 02>
```

### Buffer.alloc
返回一个大小指定的初始化的`Buffer`,
```js
 let b1 = Buffer.allocUnsafe(10) // 开一块内存空间，大小为10个字节，初始化数据。
 console.log(b1)  //<Buffer 00 00 00 00 00 00 00 00 00 00>
```


### Buffer.allocUnsafe
返回一个指定大小的新建的未初始化的 `Buffer`, 里面可能存有敏感数据。
```js
 let b1 = Buffer.allocUnsafe(10) // 开一块内存空间，大小为10个字节，没有初始化。
 console.log(b1)  //<Buffer 58 06 03 03 01 00 00 00 50 06>
```
### Buffer.concat
返回一个合并了 `list` 中所有`Buffer` 实例的新 `Buffer`。
也可以认为是拼接Buffer对象，然后返回一个新的对象。
`Buffer.concat(...args,totallength)`
* ...args :表示多个Buffer对象
* totallength： 表示总长度，如果合并的对象的长度超过了这个阈值，则截取为阈值规定的长度


## Http

```js
const http = require("http")
// req表示请求对象  res表示响应对象
let server = http.createServer((req,res)=>{
    res.write("hello") // 响应数据
    res.end() // 结束响应
})
server.listen(3000,()=>{
    console.log("服务器已启动....")
})
```
### GET请求

```js
 const http = require('http')
+   const querystring = require('querystring')
   
+   // 设置服务器端口
    const PORT = 8000

    // 创建服务器
    const server = http.createServer((req, res) => {
+       // 获取请求的url
+       const url = req.url
+       // 获取请求的method
+       const method = req.method
+       // 解析URL，把url中?后面的参数转换为对象
+       const query = querystring.parse(url.split('?')[1])
       
+       // 设置返回数据的Content-type为JSON
+       res.setHeader('Content-type', 'application/json')
    
+       if (method === 'GET') {
+           // 返回的数据
+           let resData = {
+               error: 0,
+               message: 'GET返回成功',
+               data: {
+                   query: query
+               }
+           }
+           // 将对象转换为json字符串
+           res.end(JSON.stringify(resData));
+           return
+       }
+       // 如果没有匹配，则返回404页面
+       res.writeHead(200, {'content-type': 'text/plain'});
+       res.write('404 Not Found\n')
+       res.end()

    });

    // 设置服务器端口
    server.listen(PORT)

```
### POST请求

```js
    ...（略）
    const server = http.createServer((req, res) => {
        const url = req.url
        const method = req.method
        const query = querystring.parse(url.split('?')[1])
+       const contentType = req.headers['content-type']
       
        // 设置返回数据的Content-type为JSON
        res.setHeader('Content-type', 'application/json')

        if (method === 'GET') {...}

+       if (method === 'POST') {
+           if (contentType === 'application/json') {
+               let postData = '';
+               req.on('data', chunk => {
+                   // chunk是原始二进制数据，需要转化成字符串
+                   postData += chunk.toString()
+               })
+               req.on('end', () => {
+                   res.end(postData)
+               })
+               return
+           }
+       }
       ...（略）

```

1. 通过`req.headers['content-type']`获取请求的数据格式，如果是`applicatin/json`则进入下面的逻辑。
2. 创建`postData`变量，用来存储`post`数据。
3. 在接收数据流的时候，会不断触发`request`的`data`事件，`postData`持续累积数据。
4. 当数据流接收完毕，会触发`request`的`end`事件，返回给客户端最终结果。

ps:如果接收的是表单，fromdata数据，推荐使用`multiparty`模块，用npm和yarn按照即可

```js
    const http = require('http')
    const querystring = require('querystring')
+   const multiparty = require('multiparty')
    
    const PORT = 8000

    const server = http.createServer((req, res) => {
        ...（略）
        if (method === 'GET') {...}

        if (method === 'POST') { 
            if (contentType === 'application/json') { ... }
+           else if (contentType.indexOf('multipart/form-data') !== -1) {
+               let form = new multiparty.Form()
+               form.parse(req, function(err, fields, files) {
+                   res.end(JSON.stringify({fields: fields, files: files}));
+               });
+               return
            }
        }
        ...（略）
```


## Path
`path` 模块提供用于处理文件路径和目录路径的实用工具。它可以使用以下方式访问：

```js
const path = require('path');
```
### path.basename(path, ext)
`path.basename()` 方法返回 `path` 的最后一部分，类似于 `Unix` 的 `basename` 命令。 尾部的目录分隔符将被忽略。以下为使用例子。

| 参数 | 说明 |注意事项|
| --- | --- |---|
| path（string)|路径| 在window和POSIX中，运行结果会不同。|
| ext（string)|可以选扩展名|默认为空，从后到前过滤后的字符串|
```js
path.basename('/foo/bar/baz/asdf/abcd.html');
// abcd.html
path.basename('/foo/bar/baz/asdf/abcd.html','html')
// abcd.
path.basename('/foo/bar/baz/asdf/abcd.html','.html')
// abcd
path.basename('/foo/bar/baz/asdf/abcd.html','ml')
// abcd.ht
path.basename('/foo/bar/baz/asdf/abcd.html','cd.html')
// ab
path.basename('/foo/bar/baz/asdf/abcd.html','abcd.html')
// abcd.html
//ext的顺序一定要一样，否则默认为空.最后一条指令与第一条指令等效
```

### path.extname(path)

`path.extname()`方法返回 `path`的扩展名，从最后一次出现 `.` 开始到 `path`字符串结束。如果 `path`中没有 `.` 或者除了第一个字符是 `.` 外，没有其他的 `.` 字符，则返回空字符串。

```js
path.extname('index.html');
// 返回: '.html'

path.extname('index.coffee.md');
// 返回: '.md'

path.extname('index.');
// 返回: '.'

path.extname('index');
// 返回: ''

path.extname('.index');
// 返回: ''

path.extname('.index.md');
// 返回: '.md'
```

### path.dirname 
path.dirname() 方法返回 path 的目录名，类似于 Unix 的 dirname 命令。 尾部的目录分隔符将被忽略。
```js
path.dirname('/foo/bar/baz/asdf/quux');
// 返回: '/foo/bar/baz/asdf'
```

### path.join([...path])

`path.join()` 方法使用平台特定的分隔符作为定界符将所有给定的 `path` 片段连接在一起，然后规范化生成的路径。

零长度的 `path`片段会被忽略。 如果连接的路径字符串是零长度的字符串，则返回`'.'`，表示当前工作目录。
```js
path.join('/foo', 'bar', 'baz/asdf', 'quux', '..');
// 返回: '/foo/bar/baz/asdf'

path.join('foo', {}, 'bar');
// 抛出 'TypeError: Path must be a string. Received {}'
```
### path.resolve([...path])
`path.resolve()`方法将路径或路径片段的序列解析为绝对路径。

给定的路径序列从右到左进行处理，每个后续的 `path`前置，直到构造出一个绝对路径。 例如，给定的路径片段序列：`/foo、 /bar、 baz`，调用`path.resolve('/foo', '/bar', 'baz')` 将返回 `/bar/baz`。
如果在处理完所有给定的 path 片段之后还未生成绝对路径，则再加上当前工作目录。
生成的路径已规范化，并且除非将路径解析为根目录，否则将删除尾部斜杠。
零长度的 `path`片段会被忽略。
如果没有传入 `path`片段，则 `path.resolve()`将返回当前工作目录的绝对路径。
```js
path.resolve('/foo/bar', './baz');
// 返回: '/foo/bar/baz'

path.resolve('/foo/bar', '/tmp/file/');
// 返回: '/tmp/file'

path.resolve('wwwroot', 'static_files/png/', '../gif/image.gif');
// 如果当前工作目录是 /home/myself/node，
// 则返回 '/home/myself/node/wwwroot/static_files/gif/image.gif'
```

### path.parese(path)
`path.parse()` 方法返回一个对象，其属性表示 `path`的重要元素。返回的对象具有如下的属性。

| 属性名 | 类型 |
| --- | --- |
| root | string |
| dir | string|
| base | string |
| name | string |
| ext |string|

![ea0b7df9d50d4eb31bb80fa5d157515a](https://tva1.sinaimg.cn/large/006y8mN6ly1g72xq3q6jkj30fu084q35.jpg)
```js
path.parse('/home/user/dir/file.txt');
// 返回:
// { root: '/',
//   dir: '/home/user/dir',
//   base: 'file.txt',
//   ext: '.txt',
//   name: 'file' }
```



## URL
URL 是结构化的字符串，包含多个含义不同的部分。解析后的字符串返回的 URL 对象，每个属性对应字符串的不同组成部分。
url 模块提供了两套 API 来处理 URL：一个是旧版本遗留的 API，一个是实现了 WHATWG 标准的新 API。遗留的 API 还没有被废弃，保留是为了兼容已存在的应用程序。 新的应用程序应使用 WHATWG 的 API。
WHATWG 的 API 与遗留的 API 的区别如下。 在下图中, `http://user:pass@sub.example.com:8080/p/a/t/h?query=string#hash` 上方的是遗留的 url.parse() 返回的对象的属性。 下方的则是 WHATWG 的 URL 对象的属性。

WHATWG 的 origin 属性包括 protocol 和 host，但不包括 username 或 password。

![f6e85942b1f2906bbb25839b37c6f3e8](https://tva1.sinaimg.cn/large/006y8mN6ly1g72xpxm9nyj316c0mo76e.jpg)
### req.url
### url.parse

## querystring
`querystring` 模块提供用于解析和格式化 `URL` 查询字符串的实用工具。 它可以使用以下方式访问：

```js
const querystring = require('querystring');
```
### querystring.parse
用来解析返回的数据，返回值是一个对象

```js
const http = require("http")
const url = require("url") // 把路么名和查询字符串找出来
const querystring = require("querystring") // 解析查询字符串
let server = http.createServer((req,res)=>{
    let {pathname,query} = url.parse(req.url)
    let r = querystring.parse(query)
    console.log(r)  // { name: 'xiaoming' }
    console.log(r.name)  // xiaoming
    res.write("hello") 
    res.end() 
})
server.listen(3000,()=>{
    console.log("服务器已启动....")
})
// Url {
//     protocol: null,  // 协议
//     slashes: null,  
//     auth: null,   // 认证
//     host: null,   // 主机
//     port: null,    // 端口
//     hostname: null,   // 主机名
//     hash: null,  // hash
//     search: '?name=%22wangc%22',  // 查询字符串
//     query: 'name=%22wangc%22',   // 查询字符串
//     pathname: '/abc',   // 路径名
//     path: '/abc?name=%22wangc%22',   // 路径
//     href: '/abc?name=%22wangc%22' }

```



















