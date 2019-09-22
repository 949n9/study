##  shadow-dom 是什么

顾名思义， `shadow-dom`，直译的话就是 `影子dom` ？我觉得可以理解为潜藏在黑暗中的 DOM 结构，也就是我们无法直接控制操纵的 DOM 结构。

> 其实我们常用的input、video、audio等这些元素，其实也是以组件的形式存在的，即**HTML Web Component**，这些都是得益于**Shadow DOM（影子DOM）**才能实现。

首先我们先新建一个HTML页面，然后写一个空的video标签，打开chrome浏览器，在设置中勾选

`Show user agent shadow DOM`：

![image-20190922101008228](https://tva1.sinaimg.cn/large/006y8mN6ly1g782mcu7gnj30y00catag.jpg)

然后查看网页的结构，可以发现出现了如下的结构：

![image-20190922101028222](https://tva1.sinaimg.cn/large/006y8mN6ly1g782mphn04j30nk03mdg7.jpg)



在`<video>`标签中有很多的内容，隐藏的shadow-root里面的内容就是以上视频播放器控制组件HTML结构的所在之处。

因为这里我们没有设置其他属性，如果设置了其他属性，其实里面的内容就是video标签的具体实现。



以 [w3c](http://www.w3school.com.cn/tiy/t.asp?f=html5_video) 上的一个 `<video>` 例子为例，我们仅仅是填写了一个空白的标签，再加上 `src` 属性里填上视频地址，就可以播放视频了：

![image-20190922101958610](https://tva1.sinaimg.cn/large/006y8mN6ly1g782wm20nzj30hy0dck1z.jpg)

当我们查看网页结构的时候可以看到shadow-dom里面的内容
标签内实现了譬如暂停，播放，音量控制，全屏按钮，进度条等等。
![image-20190922101912830](https://tva1.sinaimg.cn/large/006y8mN6ly1g782vss7zaj310u0goaew.jpg)



> 浏览器的开发者们意识到作为前端开发者，引用一个 `<video>` 标签的时候，每次还要写入一大堆 DOM 去控制控件的表现和行为，既不简洁也很困难。所以他们界定了这样一个界限，界定了哪些是你可以访问的，哪些实现细节是访问不到的。
>
> 那些不希望我们访问到的细节，则封装在了 `shadow-dom` 中。然而，浏览器本身却可以随意跨越这个边界。设置这样一个边界之后，浏览器的开发者们就可以在我们看不见的地方使用熟悉的web技术、同样的HTML元素去创建更多的功能，而不是像我们一样要在页面上用div和span来堆砌这些元素。

##Shadow DOM概念

Shadow DOM是HTML的一个规范 ，它允许浏览器开发者封装自己的HTML标签、CSS样式和特定的javascript代码，同时也可以让开发人员创建类似<video>这样的自定义一级标签，创建这些新标签内容和相关的的API被称为Web Component。

![image-20190922102538798](https://tva1.sinaimg.cn/large/006y8mN6ly1g7832jrrf6j30qg0f2apn.jpg)


- **document**：我们的正常文档 document 。
- **shadow-host**：Shadow DOM的容器元素，对于一个内部有 `shadow-dom` 的元素而言，它必然需要一个宿主元素，对于上面的例子而言， `<video>` 标签，就是 shadow-dom 的宿主元素。
- **shadow-root**：通过 `createShadowRoot`(下文会提及) 返回的文档片段被称为 shadow-root 。它和它的后代元素，都将对用户隐藏，但是它们是实际存在的，在 chrome 中，我们可以通常审查元素去查看它们的具体 DOM 实现。在 `<video>` 中，例如暂停，播放，音量控制，全屏按钮，进度条等都是 shadow-root 的后代。它们工作时会显示在屏幕上，但他们的 DOM 结构对用户是不可见的。
- **content**：就是上述所说的 `<video>` 中各子组件的 DOM 的具体实现。



## 如何创建shadow-dom (了解)

参考[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/attachShadow)

>  var shadowroot = element.attachShadow(shadowRootInit); 

**shadowRootInit** 如下之一

* open 指定为开放的封装模式

```js
var shadowroot = element.attachShadow({mode: 'open'}); // Returns a ShadowRoot obj
```



* close 指定为关闭的封装模式。

```js
var shadowroot = element.attachShadow({mode: 'close'}); // Returns null
```

  

### 例子

```js
// Create a class for the element
class WordCount extends HTMLParagraphElement {
  constructor() {
    // Always call super first in constructor
    super();

    // count words in element's parent element
    var wcParent = this.parentNode;

    function countWords(node){
      var text = node.innerText || node.textContent
      return text.trim().split(/\s+/g).length;
    }

    var count = 'Words: ' + countWords(wcParent);

    // Create a shadow root
    var shadow = this.attachShadow({mode: 'open'});

    // Create text node and add word count to it
    var text = document.createElement('span');
    text.textContent = count;

    // Append it to the shadow root
    shadow.appendChild(text);

    // Update count when element content changes
    setInterval(function() {
      var count = 'Words: ' + countWords(wcParent);
      text.textContent = count;
    }, 200)
  }
}

// Define the new element
customElements.define('word-count', WordCount, { extends: 'p' });
```



## shadow-dom 的兼容性

![image-20190922103924536](https://tva1.sinaimg.cn/large/006y8mN6ly1g783gt7stij31kq0liq6y.jpg)

大部分现代浏览器已经支持 `shadow-dom` ，但是能够审查 `shadow-dom` 内部 DOM 元素的只有 `chrome` 浏览器，其他浏览器仍会把这些细节隐藏。