# 交互优化

## 1. Tap事件

1. click事件的300ms延迟触发（历史原因）

2. Tap事件的原理

    ![tap](./img/tap.jpg)

    在`touchstart`时记录事件，手指位置，在`touchend`时进行比较，如果手指位置为同一个位置（或允许移动一个非常小的位移且事件间隔小于200ms），且过程中未曾触发过`touchmove`，即可认为触发了手持设备上的`click`，一般称为`tap事件`

3. 点透的bug

    ![ghost-click](./img/ghost-click.jpg)

    点击button正上方的遮罩层时，遮罩层消失后，也会触发button的click事件，原因也是click事件的延迟300ms触发。这种现象叫做`点透bug`。

## 2. Touch事件基础

### 2-1 基础知识

*触摸才是移动设备交互的核心事件*

1. 基础事件有

    * touchstart
    * touchmove
    * touchend
    * touchcancel

2. touch event的常用属性

    * clientX/clientY
    * identifier
    * pageX/pageY
    * target

3. touch事件包含的专有的触摸属性

    * touches：跟踪触摸操作的touch对象数组
    * targetTouches：特定事件目标的touch对象数组
    * changeTouches：上次触摸改变的touch对象数组

### 2-2 常见bug

在Android只会触发一次touchstart，一次touchmove，不会触发touchend。

解决方法：在touchmove中加入event.prevetDefault()，但event.preventDefault()会导致默认的行为不发生（如页面滚动）。

### 2-3 常见交互

1. 弹性滚动
2. 下拉刷新
3. 上拉加载