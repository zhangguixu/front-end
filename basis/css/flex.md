# flex布局

## 1. 概述

`display:box`是css3新添加的盒子模型属性，它的出现可以解决布局方式，经典的应用场景有：

* 垂直等高
* 水平均分
* 按比例划分

示例的html结构

```html
<article>
    <section>1</section>
    <section>2</section>
    <section>3</section>
</article>
```

## 2. box-flex(子容器)

`box-flex`的作用是让子容器按一定的规则进行划分，看各个子元素的flex值，进行相加等分。

```css
article{
    display : -moz-box;
    display : -webkit-box;
    display : box;
    width:600px;
    height:200px;
}
section:nth-child(1){
    -moz-box-flex : 3;
    -webkit-box-flex : 3;
    box-flex : 3;
    background:orange;
}
section:nth-child(2){
    -moz-box-flex : 2;
    -webkit-box-flex : 2;
    box-flex : 2;
    background:blue;
}
section:nth-child(3){
    -moz-box-flex : 1;
    -webkit-box-flex : 1;
    box-flex : 1;
    background:red;
}
```

这段代码即将三个子元素等分成（3+2+1）6份，其中第一个子元素占3，第二个占2，第三个占1，运行效果如下：

![box-flex](../../image/box-flex.png)

其他需要注意的情况：

1. 若有子容器设置了固定的宽度，则直接应用设置的宽度值，其他没有设置的则在父容器的宽度基础上减去子容器设置的固定宽度，在剩下的宽度值得基础上按照一定的比例进行分配。
2. 若有子容器设置了间隙（margin），则父容器的宽度会减去间隙值，再按比例分配。


## 3. box(父容器)

### 3.1 box-orient

用来确定父容器里子容器的排列方式，是水平还是垂直的，可选属性有

* horizontal：水平排列
* vertical：垂直排列
* inline-axis：水平排列
* block-axis：垂直排列
* inherit

1. 当设置了`horizontal`或`inline-axis`时，如果父容器定义了高度值，其子容器的高度值设置则无效，所有子容器的高度等于父容器的高度值；如果父容器不设置高度值，其子容器的高度值才有效且取最大高度值的子容器的高度

2. 当设置了`vertical`或`block-axis`时，如果父容器定义了宽度，其子容器的宽度值设置则无效；如果父容器不设置宽度值，其子容器的宽度值才有效且取最大的子容器的宽度值。

```css
article{
    width:600px;
    height:200px;
    display:-moz-box;
    display:-webkit-box;
    display:box;
    -moz-box-orient:vertical;
    -webkit-box-orient:vertical;
    box-orient:vertical;
}
section:nth-child(1){
    background:orange;
    -mox-box-flex:3;
    -webkit-box-flex:3;
    box-flex:3;
}
section:nth-child(2){
    background:blue;
    -mox-box-flex:2;
    -webkit-box-flex:2;
    box-flex:2;
}
section:nth-child(3){
    background:red;
    -mox-box-flex:1;
    -webkit-box-flex:1;
    box-flex:1;
}
```

![box-orient-vertical](../../image/box-orient-vertical.png)

### 3.2 box-direction

用来确定父容器的子容器排列顺序，可选属性

* normal
* reverse
* inherit

### 3.3 box-align

表示父容器里面子容器垂直对齐方式，可选属性

* start（居顶对齐）

    ![box-align-start](../../image/box-align-start.jpg)

* end（居底对齐）

    ![box-align-end](../../image/box-align-end.jpg)

* center（居中对齐）

    ![box-align-center](../../image/box-align-center.jpg)

* stretch（拉伸）

    ![box-align-stretch](../../image/box-align-stretch.jpg)

### 3.4 box-pack

表示父容器里面子容器的水平对齐方式，可选参数

* justify（支持性暂时不好）

* start（居左对齐）

    ![box-pack-start](../../image/box-pack-start.jpg)

* end（居右对齐）

    ![box-pack-end](../../image/box-pack-end.jpg)

* center（居中对齐）

    ![box-pack-center](../../image/box-pack-center.jpg)

## 4. 最佳实践

```css
/* ============================================================
   flex：定义布局为盒模型
   flex-v：盒模型垂直布局
   flex-1：子元素占据剩余的空间
   flex-align-center：子元素垂直居中
   flex-pack-center：子元素水平居中
   flex-pack-justify：子元素两端对齐
   兼容性：ios 4+、android 2.3+、winphone8+
   ============================================================ */
.flex{
    display:-webkit-box;
    display:-webkit-flex;
    display:-ms-flexbox;
    display:flex;
}
.flex-v{
    -webkit-box-orient:vertical;
    -webkit-flex-direction:column;
    -ms-flex-direction:column;
    flex-direction:column;
}
.flex-1{
    -webkit-box-flex:1;
    -webkit-flex:1;
    -ms-flex:1;
    flex:1;
}
.flex-align-center{
    -webkit-box-align:center;
    -webkit-align-items:center;
    -ms-flex-align:center;
    align-items:center;
}
.flex-pack-center{
    -webkit-box-pack:center;
    -webkit-justify-content:center;
    -ms-flex-pack:center;
    justify-content:center;
}
.flex-pack-justify{
    -webkit-box-pack:justify;
    -webkit-justify-content:space-between;
    -ms-flex-pack:justify;
    justify-content:space-between;
}
```
