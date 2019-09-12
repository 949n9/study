## MAGIN 属性塌陷问题

### 兄弟元素塌陷问题

当有两个上下排布的盒子，给上面的盒子设置一个`margin-bottom:90px`，给下面盒子设置一个`margin-top:10px`的时候，上下两个盒子的`margin`值并不是100,当遇到这种兄弟元素`margin`重叠时，浏览器会取最大值来作为`margin`值，所以上面的案例就是浏览器解析`margin`的最终值就是`90px`

父子元素塌陷问题

当有两个盒子，大盒子套小盒子，然后同时给大盒子设值`margin-top:100px`小盒子设置`margin-top10px`浏览器出现的效果去却是如下图

![屏幕快照 2019-09-11 下午7.32.55](/Users/949n9/Markdown/MarkPicture/屏幕快照 2019-09-11 下午7.32.55.png)

父子元素塌陷就是，子元素的`margin-top:19px`不生效了。
解决方法有三种：
* 给父元素加上padding
* 给父元素加上border
* 给父元素加上一个`overflow: hidden`属性


