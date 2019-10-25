> Less 是一门 CSS 预处理语言，它扩展了 CSS 语言，增加了变量、Mixin、函数等特性，使 CSS 更易维护和扩展。
>
>  Less 可以运行在 Node 或浏览器端。

## 变量

在CSS中常常会有一些属性值出现很多次，例如颜色，由于追求风格的一致性，会复用一些颜色。

理想状态下，我们只需要修改`bgColor`的值就可以修改多处。

```JS
const bgColor="skyblue";
$(".post-content").css("background-color",bgColor);
$("#wrap").css("background-color",bgColor);
$(".arctive").css("background-color",bgColor);

```

#### 值变量

```less
/* Less */
@color: #999;
@bgcolor: skyblue;
@width: 50%;
#wrap{
	color: @color;
	background: @bgColor;
	width: @width；
}
```

以`@`开头定义变量，使用的时候直接键入变量明就可以了。

>  在平时工作中，我们就可以把 常用的变量 封装到一个文件中，这样利于代码组织维护。

```less
@tooltip-bg                   : rgba(70, 76, 91, .9);
@head-bg                      : #f9fafc;
@table-thead-bg               : #f8f8f9;
@table-td-stripe-bg           : #f8f8f9;
@table-td-hover-bg            : #ebf7ff;
@table-td-highlight-bg        : #ebf7ff;
@menu-dark-title              : #515a6e;
```



### 嵌套

**& 的妙用**

& ：代表的上一层选择器的名字，此例便是`header`。

```less
/* Less */
#header{
  &:after{
    content:"Less is more!";
  }
  .title{
    font-weight:bold;
  }
  &_content{//理解方式：直接把 & 替换成 #header
    margin:20px;
  }
}
/* 生成的 CSS */
#header::after{
  content:"Less is more!";
}
#header .title{ //嵌套了
  font-weight:bold;
}
#header_content{//没有嵌套！
    margin:20px;
}


```

