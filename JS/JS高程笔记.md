## 序

立个flag吧，希望把这本红宝书读完，做好笔记。以后可以二次重温。



## 第二章

### 2.1  `<script>`标签

`<script>`标签有 6 个属性，一般比较常用的就是`src`,还有几个`defer`,`async`也能看到。

* async : 可选。表示应该立即下载脚本，但不应妨碍页面中的其他操作，比如下载其他资源或 等待加载其他脚本。只对外部脚本文件有效。
* defer : 可选。表示脚本可以延迟到文档完全被解析和显示之后再执行。只对外部脚本文件有 效。IE7 及更早版本对嵌入脚本也支持这个属性。
* src : 可选。表示包含要执行代码的外部文件。JSONP跨域也是用到了这个属性
* charset : 可选。表示通过 src 属性指定的代码的字符集。由于大多数浏览器会忽略它的值， 因此这个属性很少有人用。
* language : 已经废弃了，在早期版本用来表示编写代码所使用的脚本语言(JaveScript,VBscript...)
* type : 可选。可以看成是 language 的替代属性;表示编写代码使用的脚本语言的内容类型(也称为 MIME 类型)。虽然 text/javascript 和 text/ecmascript 都已经不被推荐使用，但人 们一直以来使用的都还是 text/javascript。实际上，服务器在传送 JavaScript 文件时使用的 MIME 类型通常是 application/x–javascript，但在 type 中设置这个值却可能导致脚本被忽略。另外，在非 IE 浏览器中还可以使用以下值:application/javascript 和 application/ecmascript。考虑到约定俗成和最大限度的浏览器兼容性，目前 type 属性的值依旧还是 `text/javascript`。不过，这个属性并不是必需的，如果没有指定这个属性，则其默认值仍为 text/javascript。

