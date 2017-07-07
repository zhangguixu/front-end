# 像素基础

## 1. 概念

1. px: css pixels，逻辑像素，浏览器使用的抽象单位
2. dp: device independent pixels，设备无关像素
3. dpr: device pixel ratio，设备像素缩放比
4. dpi: 打印机每英寸可以喷的墨汁点
5. ppi: 屏幕每英寸的像素数量，即单位英寸内的像素密度

## 2. 换算

1. ppi、dpr

    ![dpr-ppi](../image/dpr-ppi.jpg)

    *dpr>=2的都属于retina屏幕，retina屏幕会引起很多的移动端样式的问题*

2.  dpr、pixel、dp

    ![dpr](../image/dpr.jpg)

    公式换算：

    ![px-dp](../image/px-dp.gif)

## 3. 整体联系

![pixel](../image/pixel.jpg)