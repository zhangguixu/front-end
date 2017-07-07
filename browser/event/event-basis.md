# 事件基础概念

## 1. 背景知识

跟所有开发UI的思路一样，JavaScript与HTML之间通过`事件`来进行交互。事件，就是文档或浏览器窗口中发生的一些特定的交互瞬间，我们使用监听器（listener/hanlder）来预订事件，当事件触发时，执行相对应的代码，这种就是传统软件工程中被成为观察者模式的模型，这种模型支持页面的行为与页面的UI之间的松散耦合。

## 2. 事件流

理解事件流，我们可以从一个问题出发，那就是：页面的哪一个部分会拥有某个特定的事件？当我们点击了一个button时，点击事件不仅发生在按钮上，换句话说，在单击按钮的同时，也是单击了按钮的容器元素，甚至也单击了整个页面。

这便有了事件流的概念，它描述的就是从页面中接收事件的顺序。而事件流有两种：

* 事件冒泡
* 事件捕获

*早期，IE和Netscape的事件流顺序是相反的，IE是事件冒泡流，Netscape是事件捕获流*。

### 2.1 事件冒泡

IE的事件流叫做事件冒泡（event bubbling），即事件开始时由最具体的元素（文档中嵌套层次最深的那个节点）接收，然后逐级向上传播到较为不具体的节点。

例如：

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Event Bubbling</title>
    </head>
    <body>
        <div>click</div>
    </body>
</html>
```

当我们点击页面中的div元素时，这个click事件会按照如下的顺序传播

![event-bubbling.png](../../image/event-bubbling.png)

IE9、firefox、chrome和safari会将事件一直冒泡到window对象。

### 2.2 事件捕获

NetScape提出的另一种事件流叫做事件捕获，即是不太具体的节点应该更早接收到事件，而最具体的节点应该最后接收到事件，它的用意在于事件到达预定目的之前捕获它。

还是以上面的html代码为例子，则当我们点击页面的div元素时，这个click事件的顺序为：

![event-capturing.png](../../image/event-capturing.png)

### 2.3 DOM 事件流

`DOM2级事件`规定事件流包括三个阶段：

* 事件捕获阶段，为截获事件提供了机会
* 处于目标阶段，实际的目标接收到事件
* 事件冒泡阶段，也可以在这个阶段对事件作出响应

![DOM-eventflow.png](../../image/DOM-eventflow.png)


需要注意的有两点：

1. 尽管DOM2级事件规范要求事件应该从document对象开始传播，但是大多数浏览器都是从window对象开始捕获的。
2. DOM2级明确要求捕获阶段不会涉及事件目标，但是IE9，safari，chrome，firefox和opera9.5都会在捕获阶段触发事件对象上的事件，结果，就是有两个机会在目标对象上操作事件。

*IE8及更早版本不支持DOM事件流。*

## 3. 事件处理程序

事件是用户或浏览器自身执行的某种动作，而响应某个事件的函数就叫做事件处理程序。

设置事件处理程序的方式有4种，以下来一一介绍。

### 3.1 HTML事件处理程序

某个元素支持的事件，都可以使用一个与相应事件处理程序同名的HTML属性来指定，这个属性的值应该是能够执行的JavaScript代码，例如：

```html
<input type="button" id="btn" value="click" onclick="alert('click')" />
```

我们可以直接在属性中的js代码中通过event变量获取到event对象，代码中的this指的是事件的目标元素。

```html
<input type="button" value="click" onclick="alert(event.type);alert(this.value);" />
```

这种方式在很多古老的页面中都可以看到，它比较的简单，粗暴。但是它导致了HTML与JavaScript代码紧密耦合，从实践的角度上，最好不要采用这样的形式。

### 3.2 DOM0级事件处理程序

通过JavaScript指定的事件处理程序的传统方式，就是将一个函数赋值给一个事件处理程序属性。

这个方式有两个优点：

1. 简单
2. 跨浏览器，至今所有的现代浏览器都支持。

```javascript
var btn = document.getElementById("btn");
btn.onclick = function (event) {
    alert(event.type);
    alert(this.id);
}
```

需要注意的是，通过这个方式进行设置，我们无法对同一个元素的同一个事件绑定多个事件处理程序（会覆盖），例如以下代码，就只会执行最后设置的函数。

```javascript
var btn = document.getElementById("btn");
btn.onclick = function (event) { // 不会执行
    alert(event.type);
    alert(this.id);
}
btn.onclick = function (event) { 
    alert("覆盖了");
}
```

依据这个特性，我们可以通过`btn.onclick = null`来删除设置的事件处理程序。当然我们也可以通过以下的技巧，来为其绑定多个事件处理程序。

```javascript
var btn = document.getElementById("btn");
btn.onclick = function (event) { 
    alert(event.type);
    alert(this.id);
}

var oldHandler = btn.onclick; // 获取前面设置的事件处理程序

btn.onclick = function (event) { 
    oldHandler.call(this, event);
    alert("覆盖了");
}
```

### 3.3 DOM2级事件处理程序

`DOM2级事件`定义了两个方法，用于处理指定和删除事件程序的操作

* addEventListener(type, listener, isCapture)
* removeEventListener(type, listener, isCapture)

它们接收三个参数：

* 要处理的事件名（不需要带on，例如点击事件是`click`）
* 作为事件处理程序的函数
* 布尔值，为true时表示捕获阶段调用事件处理程序，为false则表示冒泡阶段调用事件处理程序

```javascript
var btn = document.getElementById("btn");
btn.addEventListener("click", function (event) {
    alert(event.type);
    alert(this.id);
}, false);
```

使用这个方式的主要好处就是可以添加多个事件处理程序

```javascript
var btn = document.getElementById("btn");
btn.addEventListener("click", function (event) {
    alert(event.type);
    alert(this.id);
}, false);
btn.addEventListener("click", function (event) {
    alert("hello world");
}, false);
```

通过`addEventListener`方式添加的事件处理程序只能通过`removeEventListener`来删除，这就是说如果我们采用匿名函数的方式来添加事件处理程序的话，则无法进行删除，因此比较好的方式就是：

```javascript
var btn = document.getElementById("btn");

function handler(event) {
    alert(event.type);
    alert(this.id);
}

btn.addEventListener("click", handler, false);
btn.removeEventListener("click", handler, false);
```

在大多数情况下，都是将事件处理程序添加到事件流的冒泡阶段，这样可以最大限度地兼容各种浏览器。

### 3.4 IE事件处理程序

IE实现了与DOM中类似的两个方法：

* attachEvent(type, listener)
* detachEvent(type, listener)

*参数type是on+事件名，如点击事件，则是onclick*

由于IE8及更早版本只支持事件冒泡，所以通过attachEvent()添加事件处理程序都会被添加到冒泡阶段。

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Event Bubbling</title>
    </head>
    <body>
        <input type="button" id="btn" value="click"/>
        <script type="text/javascript">
        var btn = document.getElementById("btn");
        btn.attachEvent("onclick", function (event){
            alert(event.type);
            alert(this.id); // undefined
            alert(this === window); // true
        });
        </script>
    </body>
</html>
```

上面的代码其实是有问题的，当`alert(this.id)`时，会看到结果为`undefined`，这就是使用这个方法特别要注意的地方，事件处理程序会在全局作用域中运行，因此this等于window。

我们可以运用一个小技巧，来修复这个问题。

```javascript
var btn = document.getElementById("btn");

function handler(event) {
    alert(event.type);
    alert(this.id);
}

btn.attachEvent("onclick", function (event){
    handler.call(btn, event); // 绑定作用域
});
```


其他的地方，attachEvent()就跟addEventListener()类似，都可以绑定多个事件处理程序，要删除只能调用detachEvent()，并且不能使用匿名函数的形式。

*支持IE事件处理程序的浏览器有IE和Opera*