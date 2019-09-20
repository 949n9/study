## MAGIN 属性塌陷问题

### 兄弟元素塌陷问题

当有两个上下排布的盒子，给上面的盒子设置一个`margin-bottom:90px`，给下面盒子设置一个`margin-top:10px`的时候，上下两个盒子的`margin`值并不是100,当遇到这种兄弟元素`margin`重叠时，浏览器会取最大值来作为`margin`值，所以上面的案例就是浏览器解析`margin`的最终值就是`90px`

父子元素塌陷问题

当有两个盒子，大盒子套小盒子，然后同时给大盒子设值`margin-top:100px`小盒子设置`margin-top10px`浏览器出现的效果去却是如下图

![](https://tva1.sinaimg.cn/large/006y8mN6ly1g72xlccx7uj30gm0gy0ss.jpg)

父子元素塌陷就是，子元素的`margin-top:19px`不生效了。
解决方法有三种：

* 给父元素加上padding
* 给父元素加上border
* 给父元素加上一个`overflow: hidden`属性

原理： 因为他们属于同一个BFC，所以在垂直方向上会发生margin重叠。
之所以加上`overflow: hidden`是为了产生两个不同的BFC,这样他们就不会互相重叠。

## 深入理解BFC和IFC

###BFC

BFC(Block Formatting Contexts)直译为"块级格式化上下文"。Block Formatting Contexts就是页面上的一个隔离的渲染区域，容器里面的子元素不会在布局上影响到外面的元素，反之也是如此。如何产生BFC？

float的值不为none。

overflow的值不为visible。

position的值不为relative和static。

display的值为table-cell, table-caption, inline-block中的任何一个。

那BFC一般有什么用呢？比如常见的多栏布局，结合块级别元素浮动，里面的元素则是在一个相对隔离的环境里运行。

#### 如何生成BFC？

- *当一个HTML元素满足下面条件的任何一点，都可以产生 Block Formatting Context*
- 根元素
- `float`的值不为`none`；
- `overflow`的值不为`visible`(可以为`hidden`,`scroll`,`auto`)；
- `display`的值为`inline-block`,`table-cell`,`table-caption`,`flex`,`inline-flex`中的任何一个；
- `position`的值为`absolute`,`fixed`(不为`static`,`relative`中的任何一个)；
- `display`：`table`也认为可以生成BFC，其实这里的主要原因在于`table`会默认生成一个匿名的`table-cell`，正是这个匿名的`table-cell`生成了BFC。
- 常用的用来触发BFC的CSS样式：
  `overflow: scroll`,`overflow: hidden`,`display: flex`, `float: left`,`display: table`
- 其中可能产生的一些问题
- `display:table` —— 可能会产生一些问题
- `overflow:scroll` —— 可能会显示不必要的滚动条
- `overflow:hidden` —— 将会剪切掉溢出的元素
- `float:left` —— 将会把元素置于容器的左边，其他元素环绕着它

#### BFC的布局规则

1. 内部的Box会在垂直方向上一个接一个的放置；
2. 垂直方向上的距离有`margin`决定。（完整的说法是：属于同一个BFC的两个相邻的Box的`margin`会发生重叠，与方向无关）
3. 每个盒子的左外边框紧挨着包含块的左边框（从右到左的格式，则为紧挨右边框），即使浮动元素也是如此。（这说明BFC中的子元素不会超出它的包含块）；
4. BFC的区域不会与`float`的元素区域重叠；
5. 计算BFC的高度时，浮动子元素也参与计算；
6. BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素，反之亦然；

#### BFC的作用及原理

- ###### 自适应两栏布局

```html
<style>
  body { width: 300px; } 
  .aside { width: 100px; height: 150px; float: left; background: #f66; } 
  .main { height: 200px; background: #fcc; }
</style> 
<body> 
     <div class="aside"></div> 
     <div class="main"></div>
</body>
```

- 效果图如下：
- <img src="https://tva1.sinaimg.cn/large/006y8mN6ly1g72xmfp80qj30gs0b8mx8.jpg" style="zoom:33%;" />
- 分析：

> ##### 根据BFC布局规则 第3条：

```undefined
每个盒子的左外边框紧挨着包含块的左边框（从右到左的格式，则为紧挨右边框），即使浮动元素也是如此。
```

**现象：**虽然存在浮动的元素 aside，但 main 的左边依然会与包含块的左边相接触。

- 修改：

> ##### 根据BFC布局规则第四条：

```bash
BFC的区域不会与float box重叠。
```

**改变方法：**我们可以通过触发 main 生成BFC， 来实现自适应两栏布局，当触发main 生成BFC后，这个新的BFC不会与浮动的 aside 重叠，main 会根据包含块的宽度，和 aside 的宽度，自动变窄。（一个盒子的边框会由于浮动而收缩，盒子本身将会变得更窄）

```cpp
.main { overflow: hidden;}  //或者利用上述其他方式为它生成 BFC
```

- 新的效果图如下：

<img src="https://tva1.sinaimg.cn/large/006y8mN6ly1g72xnemrxvj30gm0b2weg.jpg" style="zoom:33%;" />

- ###### 清除内部浮动

```html
<style>
    .par { border: 5px solid #fcc; width: 300px; }
    .child { border: 5px solid #f66; width: 100px; height: 100px; float: left;}
</style>
<body> 
    <div class="par"> 
      <div class="child"></div> 
      <div class="child"></div>
    </div>
</body>
```

- 效果图如下：

  

  <img src="https://tva1.sinaimg.cn/large/006y8mN6ly1g72xp3b0z9j30ho06kq33.jpg" alt="image-20190917213637915" style="zoom:33%;" />

- 修改：

> ##### 根据BFC布局规则第五条：

```css
计算BFC的高度时，浮动元素也参与计算
为达到清除内部浮动，我们可以触发 par 生成BFC，那么 par 在计算高度时，par 内部的浮动元素 child 也会参与计算。
.par { overflow: hidden;}　//或者利用上述其他方式为它生成 BFC

```

- 新的效果图如下：

  

  <img src="https://tva1.sinaimg.cn/large/006y8mN6ly1g72xpok22sj30h806qdfz.jpg" alt="image-20190917213735326" />

  

> ##### 其实以上的几个例子都体现了BFC布局规则第六条：

- BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。
  **解释：**
  因为BFC内部的元素和外部的元素绝对不会互相影响；
  因此，** 当BFC外部存在浮动时**，它不应该影响BFC内部Box的布局，BFC会通过变窄，而不与浮动有重叠；同样的，**当BFC内部有浮动时**，为了不影响外部元素的布局，BFC计算高度时会包括浮动的高度；避免margin重叠也是这样的一个道理。



####关于BFC的总结

BFC的几个用途 （但是也有其他方法可以达到这种效果）

 * 1、BFC可以阻止垂直边距叠加问题
 * 2、BFC可以包含内部元素的浮动
 * 3、BFC可以阻止元素被浮动覆盖
 * 4、BFC可以决定清除浮动的范围







**IFC**

IFC(Inline Formatting Contexts)直译为"内联格式化上下文"，IFC的line box（线框）高度由其包含行内元素中最高的实际高度计算而来（不受到竖直方向的padding/margin影响)

IFC中的line box一般左右都贴紧整个IFC，但是会因为float元素而扰乱。float元素会位于IFC与与line box之间，使得line box宽度缩短。 同个ifc下的多个line box高度会不同。 IFC中时不可能有块级元素的，当插入块级元素时（如p中插入div）会产生两个匿名块与div分隔开，即产生两个IFC，每个IFC对外表现为块级元素，与div垂直排列。

那么IFC一般有什么用呢？

水平居中：当一个块要在环境中水平居中时，设置其为inline-block则会在外层产生IFC，通过text-align则可以使其水平居中。

垂直居中：创建一个IFC，用其中一个元素撑开父元素的高度，然后设置其vertical-align:middle，其他行内元素则可以在此父元素下垂直居中。

[IFC参考链接](https://juejin.im/entry/587c41f91b69e6006bf1b8ec)



##  margin,paddin设置百分比问题

可以对元素的margin设置百分数，百分数是相对于父元素的width计算，不管是margin-top/margin-bottom还是margin-left/margin-right。（padding同理）

如果没有为元素声明width，在这种情况下，元素框的总宽度包括外边距取决于父元素的width，这样可能得到“流式”页面，即元素的外边距会扩大或缩小以适应父元素的实际大小。如果对这个文档设置样式，使其元素使用百分数外边距，当用户修改浏览窗口的宽度时，外边距会随之扩大或缩小。

为什么margin-top/margin-bottom的百分数也是相对于width而不是height呢？

**CSS权威指南中的解释：**

>  我们认为，正常流中的大多数元素都会足够高以包含其后代元素（包括外边距），如果一个元素的上下外边距时父元素的height的百分数，就可能导致一个无限循环，父元素的height会增加，以适应后代元素上下外边距的增加，而相应的，上下外边距因为父元素height的增加也会增加，如果循环。

## 重绘和回流

