## 图片懒加载

## 1. 原理

当要使用到图片时，再加载图片，而不是一下子加载完所有的图片的方式，来提高页面其他图片的加载速度。

## 2. 场景

长页面中不可见区域的图片可以先不加载。

![lazy-load](../../image/lazyload.png)

## 3. 实现

思路很简单，一般都是在页面上添加一个滚动条事件，判断图片位置与浏览器顶部的距离是否小于（可视高度+滚动距离），如果小于则优先加载。

1. 获取对象距离页面顶端的距离

    ```javascript
    function getH(obj){
        var h = 0;
        while(obj){
            h += obj.offsetTop;//距离上一个标签的距离
            obj += obj.offsetParent;
        }
        return h; //文档坐标
    }
    ```

2. 判断是否在可视区域内

    ```javascript
    function isVisible(obj){
        var t = document.documentElement.clientHeight +
            (document.documentElement.scrollTop || document.body.scrollTop);
        var h = getH(obj);
        return (h < t);
    }
    ```

3. 绑定滚动事件，进行判断那些元素在可视区域内，然后进行图片加载

    ```javascript
    window.onscroll = function(){}
    ```

4. 在页面onload的时候，触发一次onscroll

    ```javascript
    window.onload = function(){
        window.onscroll();
    }
    ```