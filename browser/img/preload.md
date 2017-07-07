# 图片预加载

## 1. 原理

通过CSS或者JavaScript，先请求图片到本地，再利用浏览器的缓存机制，当要使用图片时（图片路径一致），浏览器直接从本地缓存获取到图片，加快图片的加载速度。

## 2. 场景

背景，幻灯片，相册等，将要展示的前一张和后一张优先下载

## 3. 实现

1. css的实现方式

    ```css
    #preload {background : url(01.png) no-repeat -9999px -9999px;}
    ```

    使用这个方法加载图片会同页面的其他内容一起加载，增加了页面的整体加载时间。

2. JavaScript的实现方式

    ```javascript
    var img = document.createElement('img');
    img.src = '01.png'; //就会开始下载，不用添加到dom树中
    ```

    ```javascript
    var img = new Image();
    img.src = '01.png'; //原理同上,只不过无法将其添加到DOM树。
    ```