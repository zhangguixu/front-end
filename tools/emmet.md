# emmet

## 概述

Emmet的前身是大名鼎鼎的Zen coding，如果你从事Web前端开发的话，对该插件一定不会陌生。它使用仿CSS选择器的语法来生成代码，大大提高了HTML/CSS代码编写的速度

## 简易指南

### html

1. 快速构建html结构

    ```shell
    html:5 + tab
    ```

    可以快速生成

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Document</title>
    </head>
    <body>
    </body>
    </html>
    ```

2. 添加class、id

    ```shell
    p.foo + tab
    p#bar + tab
    p.foo#bar + tab
    ```

    效果

    ```html
    <p class="foo"></p>
    <p id="bar"></p>
    <p class="bar" id="foo"></p>
    ```

3. 添加属性（class和id也可以按照这种形式）

    ```shell
	a[href=gitbook.com]  +  tab
    ```

    效果

    ```html
    <a href="gitbook.com"></a>
    ```

4. 添加元素内容

    ```shell
    h1{foo} +  tab
    ```

    效果

    ```html
    <h1>foo</h1>
    ```

5. 标签的父子关系、相邻关系

    ```shell
    ul>li + tab
    h1+h2 + tab
	p>span^p + tab
    ```

    效果

    ```html
    <ul>
        <li></li>
    </ul>
    <h1></h1>
    <h2></h2>
    <p><span></span></p>
    <p></p>
    ```

6. 生成多个子标签

    ```shell
    ul>li*3>a
    ```

    效果

    ```html
    <ul>
        <li><a href=""></a></li>
        <li><a href=""></a></li>
        <li><a href=""></a></li>
    </ul>
    ```

7. 隐式标签,自动根据上下文

    * li：用于ul和ol中
	* tr：用于table、tbody、thead和tfoot中
	* td：用于tr中
	* option：用于select和optgroup中

    ```shell
    .active>a + tab
    ```

    效果

    ```html
    <ul>
        <li class="active"><a href=""></a></li>
    </ul>
    ```

8. 分组

    ```shell
    (.foo>h1)+(.bar>h2) + tab
    ```

    效果

    ```html
    <div class="foo">
        <h1></h1>
    </div>
    <div class="bar">
        <h2></h2>
    </div>
    ```

9. 定义多个带属性的元素

    ```shell
    ul>li.item$*3 + tab
    ```

    效果

    ```html
    <ul>
        <li class="item1"></li>
        <li class="item2"></li>
        <li class="item3"></li>
    </ul>
    ```

### css

```shell
w100   +  tab
h100   +  tab
p10    +  tab
m10    +  tab
w10p   +  tab
w10e   +  tab
```

```css
.demo{
	width: 100px;
	height: 100px;
	padding: 10px;
	margin: 10px;
	width: 10%;
	width: 10em;
}
```

```shell
@f   +  tab
@f+  +  tab
b+   +  tab
f+   +  tab
```

```css
@font-face {
	font-family:;
	src:url();
}

@font-face {
	font-family: 'FontName';
	src: url('FileName.eot');
	src: url('FileName.eot?#iefix') format('embedded-opentype'),
		 url('FileName.woff') format('woff'),
		 url('FileName.ttf') format('truetype'),
		 url('FileName.svg#FontName') format('svg');
	font-style: normal;
	font-weight: normal;
}

.demo{
	background: #fff url() 0 0 no-repeat;
	font: 1em Arial,sans-serif;
}
```

## 学习资料

1. [emmet](http://docs.emmet.io/cheat-sheet/)