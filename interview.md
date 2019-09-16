# interview

### cookie, sessionstorage, localstorage, session是什么？

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