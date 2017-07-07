# 特殊样式处理

## 1. 高清图片

在移动web页面上渲染图片，为了避免图片产生模糊，图片的宽高应该用物理像素单位渲染，即是`100*100px`的图片就应该使用`100*100dp`。

```css
.demo {
    width : (w_value/dpr)px;
    height : (h_value/dpr)px;
}
```

## 2. 1像素边框

这个问题同样是retina屏幕导致的，根本原因是`1px的边框使用2dp的渲染`。

## 3. 相对单位rem

* em在多次嵌套下会变的很难计算。
* rem能作为全局统一设置的度量。

不适用rem的情况：font-size

一般来讲font-size是不应该使用rem等相对单位的，因为字体的大小是趋向于阅读的实用性，并不适合参与排版布局。

## 4.  多行文本溢出

1. 单行文本溢出

    ```css
    .inaline{
        overflow : hidden;
        white-space : nowrap;
        text-overflow : ellipsis;
    }
    ```

2. 多行文本溢出

    ```css
    .intwoline{
        display : -webkit-box !important;
        overflow : hidden;

        text-overflow : ellipsis;
        word-break : break-all;

        -webkit-box-orient : vertical;
        -webkit-line-clamp : 2; /*行数*/
    }
    ```