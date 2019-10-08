## 申请百度账号和ak









## Hello Map demo

```html
<!DOCTYPE html>
<html>
<head>
    <meta name="viewport" content="initial-scale=1.0, user-scalable=no"/>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
    <title>Hello, World</title>
    <style type="text/css">
        html {
            height: 100%
        }

        body {
            height: 100%;
            margin: 0px;
            padding: 0px
        }

        #container {
            height: 50%;
            width: 50%;
            margin: 0 auto ;
            border: 1px solid black;
        }
    </style>
    <script type="text/javascript" src="http://api.map.baidu.com/api?v=3.0&ak=aiCu8E2qzXy5tbiHoMFqf662iARrqmrw">
        //v3.0版本的引用方式：src="http://api.map.baidu.com/api?v=3.0&ak=您的密钥"
    </script>
</head>

<body>
<div id="container"></div>
<script type="text/javascript">
    var map = new BMap.Map("container");
    // 创建地图实例
    var point = new BMap.Point(116.404, 39.915);
    // 创建点坐标
    map.centerAndZoom(point, 15);
    // 初始化地图，设置中心点坐标和地图级别
</script>
</body>
</html>>

```







## 控件

```js
map.addControl(new BMap.NavigationControl());    
map.addControl(new BMap.ScaleControl());    
map.addControl(new BMap.OverviewMapControl());    
map.addControl(new BMap.MapTypeControl()); 
```

