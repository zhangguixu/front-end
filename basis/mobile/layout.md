# 移动web布局

## 1. Flexbox弹性布局

| flex | flex-box[兼容性较好] |
| :--- | :------- |
| diplay:-webkit-flex | display:-webkit-flex-box|
| -webkit-flex:1 | -webkit-flex-box-flex:1|
| justify-content:center | box-pack:center |
|align-items:center| box-align:center|

## 2. 响应式布局

**响应式设计的核心**

```css
@media screen and (max-width:1024px){
    /**/
}
```

1. 媒体类型：screen,print,handheld,all
2. 常用查询属性

    * width/height
    * devive-width/device-height
    * orientation(landscape/portrait)

**响应式设计的点**

1. 百分比布局

    如果仅仅使用媒体查询来适应不同的固定宽度设计，只会从一组css到另一组css的切换，两种之间没有任何平滑渐变，当没有命中媒体查询时，表现就会变得不可控制。

2.  弹性图片

    ```css
    img{
        max-width:100%;
    }
    ```

3. 重新布局，显示与隐藏

    1. 同比例缩减元素尺寸
    2. 调整页面结构布局
    3. 隐藏冗余的元素

    *经常需要切换位置的元素可以使用绝对定位，减少重绘，提高渲染性能*

4.  响应式设计的优缺

    根据响应式设计的理念，一个页面需要包含所有不同的设备的屏幕的样式和图片，当一个移动设备访问一个响应式的页面时，就会下载其他不同设备的冗余样式。