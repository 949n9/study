## transfrom

### 2D转换之移动 translate

> `tanslate`对于行内元素无效，就是<span>之类的。









![image-20191107173306933](https://tva1.sinaimg.cn/large/006y8mN6gy1g8plxhra3qj30ry0imjrj.jpg)

可以看到如果我们使用了`transfrom:tranlate()`后，使用了的那个元素会改变位置，不会影响其他元素，可以相当于有个占位元素在那里。不会脱离标准文档流。



#### 水平垂直居中

`translate`属性可以设置百分比，设置的百分比是按自身的宽度或者高度。一般的应用：

* 水平垂直居中

```
.box1{
	position: absolute;
	top:50%
	left: 50%
	width: 100px;
	height: 100px;
	transiform: translate(-50%,-50%)
}
```



###  2D转换之旋转 rotate

写法：

```CSS
transform: rotate(90deg)
/* deg是度数的单位，顺时针方向 */
```













#### 小三角应用

使用到的有`::after`伪元素和`transform: rotate(度数)`来实现，

```
    .box{
        margin: 100px;
        width: 100px;
        height: 100px;
        background-color: #e6a23c;
        position: relative;
    }
    .box::after{
        content: '';
        position: absolute;
        top: 0px;
        right: 0px;
        width: 15px;
        height: 15px;
        border-left: 1px solid #eee;
        border-bottom: 1px solid #eee;
        transform: rotate(-44deg);
    }
```

以后可以不使用iconfont，可以直接使用这个旋转特性，加边框。

![image-20191108174722296](https://tva1.sinaimg.cn/large/006y8mN6gy1g8qrymz7w9j302201j3y9.jpg)















### 2D转换之缩放 scale



### z-index 问题

回忆一下CSS中的Stacking Context(堆叠上下文)。因为只有了解清楚了这个概念，才能更好的了解下面的内容。

任何HTML文档默认的堆叠上下文都是`<html>`元素。因此，除非创建新的堆叠上下文。默认情况下，元素的堆叠顺序相对于页面内的其他元素。在一个未做堆叠顺序更换的页面中，其顺序就是根据HTML中的元素出现的先后顺序来决定，先出现的在底下，后出现的在顶部。用数字来表示的话是就`1,2,3,4,...,n`这样的顺序。

 

第二个`div`做了一个`margin-top`的`-50px`，可以看到第二个`div`遮住了第一个`div`。那么怎么才能改变默认的堆叠顺序呢？

先把结论给大家抛出来，**在CSS中可以使用z-index和transform可以改变元素的堆叠顺序**。但也可能会导致一些奇怪的情况，比如具有较大的`z-index`的元素并不总是位于具有较低`z-index`元素的上方。比如，在一些情况之下，同时使用`z-index`和`transform`会让`z-index`失效等。

实际上我们的网页显示的空间是三维的。下图是个展示，实际上y轴应该是越往下越大，z轴越往屏幕外越大
![image-20191107174822451](https://tva1.sinaimg.cn/large/006y8mN6gy1g8pmdc54bpj30dr091758.jpg)

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<style>
    body{
        margin: 100px;
        padding: 0;
        transform-style:preserve-3d;
    }
    .box1{
        width: 200px;
        height: 200px;
        border: 2px dashed black;
        background-color: #fff;
        transform: translateZ(2px);
    }
    .box2{
        width: 200px;
        height: 200px;
        background-color: yellowgreen;
        transform: translate(130px,-90px);
    }
    .box3{
        width: 200px;
        height: 200px;
        background-color: #409eff;
    }

</style>
<body>
    <div class="box1"></div>
    <div class="box2"></div>
    <div class="box3"></div> 
</body>
</html>
```

通过给父元素设置  `  transform-style:preserve-3d;`，`transform`就可以通过它的`translateZ()`来改变元素的层叠顺序



参考链接：https://www.cnblogs.com/reaf/p/5788781.html

