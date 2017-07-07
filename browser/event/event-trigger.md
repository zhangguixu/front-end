# 事件触发

## 1. 脚本触发

### 1.1 概述

事件触发一般会发生在：用户交互时、浏览器行为发生时、网络活动时。

这里讨论的是用脚本来触发事件，当我们触发一个处理函数时，还要确保以下的几点：

* 触发绑定在该元素上的目标处理程序
* 让DOM事件进行冒泡，并触发其他的处理程序
* 触发该模板元素的默认行为

## 1.2 代码实现

前置知识：[跨浏览器的事件监听函数](event-listener.md)

```javascript
function triggerEvent(el, event){
    var data = getData(el),
        parent = el.parentNode || el.ownerDocument; // 获取父元素用于冒泡

    if(typeof event == "string"){
        event = {type : event, target : el};
    }

    event = fixEvent(event);

    // 如果之前绑定了事件处理程序了，则执行
    if(data.dispatcher){
        data.dispatcher.call(el, event);
    }

    // 除非禁止冒泡，不然则一直递归调用
    if(parent && !event.isPropagationStopped()){
        triggerEvent(parent, event);
    } else if (!parent && !event.isDefaultPrevented()){// 触发默认行为
        var targetData = getData(event.target);
        if(event.target[event.type]){ // el.focus()?
            targetData.disabled = true; // 先禁用
            event.target[event.type](); // 执行默认行为
            targetData.disabled = false;
        }
    }
}
```

## 2. 自定义事件

自定义事件其实是观察者模式的一个很好的实践，通过自定义事件和事件绑定处理程序的方式来为我们的程序代码进行解耦，是一个很实用的编程技巧。

下面，我们通过一个例子来理解一下这样做的好处。

### 2.1 ajax示例

在一个页面上，我们希望在ajax请求开始时，显示一个转轮的gif动画，并且在请求结束时将动画进行隐藏，以便在处理请求时给用户一些反馈。

如果有一个事件为`ajax-start`，另一个事件为`ajax-complete`，那么我们就可以给这两个事件绑定事件处理程序，这样就可以轻松做到以上的要求了。

```javascript
var body = document.body;
var gif = document.createElement("img");
gif.src = "1.gif";


addEvent(body, "ajax-start", function(e){
    this.appendChild(gif);
});

addEvent(body, "ajax-complete", function(e){
    this.removeChild(gif);
});
```

那么现在有一个问题，就是这些事件并不存在，也就是说，我们需要自定义这样两个事件`ajax-start`和`ajax-complete`，并且，我们需要用脚本来触发，模拟一个事件回调的机制。

```javascript
// 一个发送ajax请求的接口
function ajax(url){
    // 创建，发送请求之前，执行回调
    triggerEvent(body, "ajax-start");

    // 成果返回
    ajax.success(function(){
        triggerEvent(body, "ajax-complete");
    });
}
```

## 来源

1. 《JavaScript忍者秘籍》