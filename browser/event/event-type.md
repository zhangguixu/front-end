# 事件类型

在`DOM3级事件`规定了以下几类事件：

* UI事件(UIEvents)，当用户与页面上的元素交互时触发
* 焦点事件，当元素获得或失去焦点时触发
* 鼠标事件(MouseEvents)，当用户通过鼠标在页面上执行操作时触发
* 滚轮事件()，当使用鼠标滚轮时触发
* 文本事件，当在文档中输入文本时触发
* 键盘事件(KeyboardEvent)，当用户通过键盘在页面上执行操作时触发
* 合成事件，当为IME输入字符时触发
* 变动事件(MutationEvents)，当底层DOM结构发生变化时触发

不同的事件类型具有不同的信息。此外，有些浏览器还会在DOM和BOM中实现其专有事件，HTML5还定义了一组事件。

我们可以通过`document.createEvent(type)`来创建一个事件（已有的事件或者自定义事件都可以）

```javascript
// 创建一个事件
var event = document.createEvent("Event");

// 初始化事件的相关信息
event.initEvent("build", true, true);

// 元素可以监听这个事件
el.addEventListener("build", function(e){
    // 做处理
}, false);

// 需要在脚本中触发事件，就会执行相对应的回调
el.dispatchEvent(event);
```

## 1. UI事件

常见的UI事件有

* load/unload
* abort
* error
* select
* resize
* scroll

### 1.1 load/unload 事件

会触发load事件的情况：

* 页面完全加载后（包括所有外部资源），会触发window上面的load事件
* 图像的加载也会触发load事件，无论是&lt;img&gt;标签还是Image对象，但要注意的是在IE8及之前版本不会生成event对象。
* 在IE9+、firefox、opera、chrome、safari3+等浏览器中，&lt;script&gt;也会触发load事件，以便开发人员确定动态加载的js文件是否加载完毕。
* IE和Opera还支持&lt;link&gt;元素上的load事件

与load事件对应就是unload事件，这个事件在文档被完全卸载后触发，其实用户从一个页面切换到另一个页面就会发生unload事件，而利用这个事件最多的情况是清除引用，以避免内存泄漏。

另外说明一点，通常我们会在页面中看到：

```html
<!DOCTYPE html>
<html>
    <head>
        <title>example</title>
    </head>
    <body onload="" onunload="">
        
    </body>
</html>
```

一般来说，在window上面发生的任何事件都可以在body元素中通过相应的属性来指定，因为在HTML中无法访问window对象，实际上，这只是为了保证向后兼容的一种权宜之计，但所有浏览器都能很好地支持这种方式。

### 1.2  resize事件

当浏览器窗口被调整到一个新的高度或宽度时，就会触发resize事件，这个事件在window上面触发。

浏览器窗口最小化或最大化时也会触发resize事件。

resize事件可能会被频繁触发，浏览器会有一些机制来保证两个resize事件触发有一定的间歇（例如20ms），我们自己在处理的时候也要特别注意，详情见[函数节流](../../javascript/skill/throttle.md)。

### 1.3 scroll事件

* scroll事件是在window对象上发生的，但它实际表示的则是页面中相应元素的变化。
* 在混杂模式下，可以通过body元素的`scrollLeft`和`scrollTop`来监控这一变化。
* 在标准模式下，除了safari（仍然是通过body），所有浏览器都会通过html元素来反应这一变化。

```javascript
window.addEventListener("scroll", function(e){
    if(document.compatMode == "CSS1Compat"){
        alert(document.documentElement.scrollTop);
    } else {
        alert(document.body.scrollTop);
    }
}, false);
```

需要注意的是，scroll事件在文档被滚动期间会被重复地触发，跟resize的处理类似。

## 2. 焦点事件

焦点事件会在页面元素获得或失去焦点时触发，利用这些事件并与

* document.hasFocus()
* document.activeElement

就可以知晓用户在页面上的行踪。

常见焦点事件共有：

* blur：在元素失去焦点时触发，这个事件不会`冒泡`，所有浏览器都支持
* focus：在元素获得焦点时触发，这个事件不会`冒泡`，所有浏览器都支持
* focusin：在元素获得焦点时触发，这个事件与HTML事件focus等价，但是会冒泡。支持的浏览器有IE5.5+、opera 11.5+、chrome、safari 5.1+
* focusout：在元素失去焦点时触发，这个事件与HTML事件blur等价，支持的浏览器有IE5.5+、opera 11.5+、chrome、safari 5.1+

*DOMFocusIn和DOMFocusOut只有Opera支持，这里就不介绍。*

当焦点从页面中的一个元素移动到另一个元素时，会依次触发下列事件：

* focusout在失去焦点的元素中触发
* focusin在获得焦点的元素中触发
* blur在失去焦点的元素上触发
* focus在获得焦点的元素中触发

## 3. 鼠标与滚轮事件

鼠标事件是Web开发中最常用的一类事件，常见的鼠标事件有：

* click：在用户单击鼠标按钮或者按下回车键时触发。
* dbclick：在用户双击鼠标按钮时触发，在DOM3级事件纳入了标准。
* mousedown：在用户按下了任意鼠标按钮时触发。

* mouseenter：在鼠标光标从元素外部`首次`移动到元素范围之内触发，这个事件不冒泡，而且在光标移动到后代元素上不会触发。
* mouseleave：

## 参考

1. 《JavaScript高级程序设计》
2. [document.createEvent()](https://developer.mozilla.org/en-US/docs/Web/API/Document/createEvent)