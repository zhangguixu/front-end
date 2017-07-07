# Web 字体与文本 

## 1. Web字体扩展

### 1.1 @font-face

一般情况下，默认的字体是可以满足网站的需求，但是对于一些需要设计感较强的场景，就需要引入自定义的字体了（在之前的解决方案，要么就是图片替换，或者是SVG，flash等）。

```css
@front-face{
    font-family : "myfont";
    src : local("myfont"), url(myfont.ttf), url(myfont.woff);
    font-weight : bold;
    font-style : italic;
}
```

1. font-family : 声明你在css中引入这个字体的名称
2. src : 可以指向单一字体文件或逗号分隔的不同字体列表，浏览器会遍历直到遇到可以识别的格式。字体可以是本地的local()，也可以是外部下载的字体url()。
3. font-weight : 可以设置bold，但是要包括一份bold版本的字体，如果没有可以不写或设置normal。
4. font-style : 同样可以设置italic，前提是存在。如果不存在可以不写。

定义了字体之后，就可以在后续的css中使用，示例如下

```css
h1 {
    font-family : "myfont";
    font-size : 18px;
}
```

### 1.2 字体格式的兼容性

各个浏览器商在字体文件格式的协议上并不能达成一致，因此，需要了解各个字体格式的支持情况

|浏览器|@font-face|ttf|otf|eot|svg|woff|
|:--|:--|:--|:--|:--|:--|:--|
|opera|10+|√|√|X|X|√|
|firefox|3.5+|√|√|X|X|√|
|safari|3.1+|√|√|X|√|√|
|chrome|4+|√|√|X|√|√|
|ie|eot4+\woff9+|X|X|√|X|√|
|ios|svg4.1+|√|√|X|√|√|
|android|2.2+|√|√|X|√|X|

现代所有浏览器已经同意支持woff，因此在未来可以实现一定的统一。

### 1.3 好处与代价

web-font可以给网站带来很多的设计感，但是也存在一些问题

1. 闪烁的无样式文本

    这个问题说的是一些浏览器对web字体的处理的行为方式，也就是说，浏览器在第一次渲染文本时没有应用web字体，待下载完字体文件后再使用web字体渲染文本。就会导致闪烁。解决的方式就是在字体文件之后，再应用该font-family。

2. 更多的带宽

    字体文件还是非常庞大的，会带来不小的带宽消耗。如果仅仅是使用web字体做logo之类的，对字体文件进行裁剪，只引入logo等所使用的那几个字体，这样可以减少字体文件的大小，从而提高下载速度。

3. 渲染问题

    在windows的IE，对web-font的渲染就非常糟糕。

### 1.4 较好的实践

防弹式的@font-face

```css
@font-face {
    font-family : "myfont";
    src : url("myfont.eot");
    src : url("myfont.eot") format("embedded-opentype"),
          url("myfont.woff") format("woff"),
          url("myfont.ttf") format("truetype"),
          url("myfont.svg#myfont") format("svg");
    font-weight : normal;
    font-style : normal;
}
```

1. IE9+支持woff格式也支持eot，因此要避免两种字体都被下载，从而浪费带宽。
2. 不同域的服务器上的web字体在firefox和ie上加载失败，可能是由于更严格的同源策略。
3. 不要为不使用的字体去声明@font-face，因为IE6~8会下载

## 2. 增强文本效果