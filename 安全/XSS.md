

## 什么是XSS？

**XSS**, 中文译名叫做： 跨站脚本攻击，英文**Cross Site Script** ，本来应该叫做**CSS**，但是因为和层叠样式表**CSS(Cascading Style Sheet)**区分开来，就改成叫做 **XSS**

> 这是一种网站应用程式的安全漏洞攻击，是[代码注入](https://www.wikiwand.com/zh-hans/代碼注入)的一种。它允许恶意使用者将程式码注入到网页上，其他使用者在观看网页时就会受到影响。这类攻击通常包含了 HTML 以及使用者端脚本语言。

XSS攻击分成三种：

**反射型**

  反射型 XSS 只是简单地把用户输入的数据 “反射” 给浏览器，这种攻击方式往往需要攻击者诱使用户点击一个恶意链接，或者提交一个表单，或者进入一个恶意网站时，注入脚本进入被攻击者的网站。

例如：当你进入一个网站，网站中有恶意链接，当你点击了这个链接，恶意链接的地址指向了 `localhost:8001/?q=111&p=222`

![image-20190921213744008](https://tva1.sinaimg.cn/large/006y8mN6ly1g77gvi4uj3j30zy0lk426.jpg)

我再启一个简单的 Node 服务处理恶意链接的请求：

```js
const http = require('http');
function handleReequest(req, res) {
    res.setHeader('Access-Control-Allow-Origin', '*');
    res.writeHead(200, {'Content-Type': 'text/html; charset=UTF-8'});
    res.write('<script>alert("反射型 XSS 攻击")</script>');
    res.end();
}

const server = new http.Server();
server.listen(8001, '127.0.0.1');
server.on('request', handleReequest);
```

当用户点击恶意链接时，页面跳转到攻击者预先准备的页面，会发现在攻击者的页面执行了 js 脚本。

![image-20190921214007561](https://tva1.sinaimg.cn/large/006y8mN6ly1g77gxz154oj30zq096q40.jpg)

这样就产生了反射型 XSS 攻击。攻击者可以注入任意的恶意脚本进行攻击，可能注入恶作剧脚本，或者注入能获取用户隐私数据(如cookie)的脚本，这取决于攻击者的目的。



**存储型**

存储型 XSS 会把用户输入的数据 "存储" 在服务器端，当浏览器请求数据时，脚本从服务器上传回并执行。这种 XSS 攻击具有很强的稳定性。

比较常见的一个场景是攻击者在社区或论坛上写下一篇包含恶意 JavaScript 代码的文章或评论，文章或评论发表后，所有访问该文章或评论的用户，都会在他们的浏览器中执行这段恶意的 JavaScript 代码。

简单来说就是把标签和标签中的脚本一起存进数据库，然后页面渲染的时候会启动数据库中存的脚本，导致XSS攻击

**基于DOM型**

基于 DOM 的 XSS 攻击是指通过恶意脚本修改页面的 DOM 结构，是纯粹发生在客户端的攻击。

## XSS 攻击的防范

现在主流的浏览器内置了防范 XSS 的措施，例如 [CSP](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CSP)。但对于开发者来说，也应该寻找可靠的解决方案来防止 XSS 攻击。

### HttpOnly 防止劫取 Cookie

HttpOnly 最早由微软提出，至今已经成为一个标准。浏览器将禁止页面的Javascript 访问带有 HttpOnly 属性的Cookie。

上文有说到，攻击者可以通过注入恶意脚本获取用户的 Cookie 信息。通常 Cookie 中都包含了用户的登录凭证信息，攻击者在获取到 Cookie 之后，则可以发起 Cookie 劫持攻击。所以，严格来说，HttpOnly 并非阻止 XSS 攻击，而是能阻止 XSS 攻击后的 Cookie 劫持攻击。

### 输入检查

**不要相信用户的任何输入。** 对于用户的任何输入要进行检查、过滤和转义。建立可信任的字符和 HTML 标签白名单，对于不在白名单之列的字符或者标签进行过滤或编码。

在 XSS 防御中，输入检查一般是检查用户输入的数据中是否包含 `<`，`>` 等特殊字符，如果存在，则对特殊字符进行过滤或编码，这种方式也称为 XSS Filter。

而在一些前端框架中，都会有一份 `decodingMap`， 用于对用户输入所包含的特殊字符或标签进行编码或过滤，如 `<`，`>`，`script`，防止 XSS 攻击：

```
// vuejs 中的 decodingMap
// 在 vuejs 中，如果输入带 script 标签的内容，会直接过滤掉
const decodingMap = {
  '&lt;': '<',
  '&gt;': '>',
  '&quot;': '"',
  '&amp;': '&',
  '&#10;': '\n'
}
```

### 输出检查

用户的输入会存在问题，服务端的输出也会存在问题。一般来说，除富文本的输出外，在变量输出到 HTML 页面时，可以使用编码或转义的方式来防御 XSS 攻击。例如利用 [sanitize-html](https://github.com/punkave/sanitize-html) 对输出内容进行有规则的过滤之后再输出到页面中。