# 跨浏览器的事件处理程序

前置知识

1. [事件基础知识](./event-basis.md)
2. [event对象](./event.md)

## 1. 初步实现

在《JavaScript高级程序设计》中提供了一个EventUtil的对象，里面实现了一个跨浏览器的事件绑定的API

```javascript
var EventUtil = {
    addHandler : function (el, type, handler) {
        if(el.addEventListener) {
            el.addEventListener(type, handler, false);
        } else if (el.attachEvent)(
            el.attachEvent("on" + type, handler);
        ) else {
            el["on" + type] = handler;
        }
    },
    removeHandler : function (el, type, handler) {
        if(el.removeEventListener) {
            el.removeEventListener(type, handler);
        } else if (el.detachEvent) {
            el.detachEvent("on" + type, handler);
        } else {
            el["on" + type] = null;
        }
    }
}
```

这是实现其实较为的简单直观，但是对于IE浏览器的处理其实有不好的地方，例如我们都知道attachEvent()中的事件处理程序会在全局作用域下执行，那么函数中的this就会指向window对象，这是一个问题，当然我们也可以对handler进行处理，绑定handler的函数作用域。此外，EventUtil并没有对event对象进行处理，因此传入handler的event也需要做兼容性处理，在封装方面做的就不好，编写handler时需要注意的地方就比较多。

```javascript
var handler = function (event) {
    // 对event对象做兼容性处理，例如获取target等
};

// 绑定函数作用域
handler = handler.bind(el); 
```

## 2. 更好的实现

下面是Dean Edward的实现，这也是jquery所借鉴的，抛弃掉attachEvent方法，直接使用跨浏览器的实现方式，即`el.onXXX = handler`，这种方式的确定就是无法绑定多个，会进行覆盖，但是可以利用一些技巧来弥补。

```javascript
// written by Dean Edwards, 2005
// with input from Tino Zijdel, Matthias Miller, Diego Perini
// http://dean.edwards.name/weblog/2005/10/add-event/

function addEvent(element, type, handler) {
	if (element.addEventListener) {
		element.addEventListener(type, handler, false);
	} else {
		// assign each event handler a unique ID
		if (!handler.$$guid) handler.$$guid = addEvent.guid++;
		// create a hash table of event types for the element
		if (!element.events) element.events = {};
		// create a hash table of event handlers for each element/event pair
		var handlers = element.events[type];
		if (!handlers) {
			handlers = element.events[type] = {};
			// store the existing event handler (if there is one)
			if (element["on" + type]) {
				handlers[0] = element["on" + type];
			}
		}
		// store the event handler in the hash table
		handlers[handler.$$guid] = handler;
		// assign a global event handler to do all the work
		element["on" + type] = handleEvent;
	}
};
// a counter used to create unique IDs
addEvent.guid = 1;

function removeEvent(element, type, handler) {
	if (element.removeEventListener) {
		element.removeEventListener(type, handler, false);
	} else {
		// delete the event handler from the hash table
		if (element.events && element.events[type]) {
			delete element.events[type][handler.$$guid];
		}
	}
};

function handleEvent(event) {
	var returnValue = true;
	// grab the event object (IE uses a global event object)
	event = event || fixEvent(((this.ownerDocument || this.document || this).parentWindow || window).event);
	// get a reference to the hash table of event handlers
	var handlers = this.events[event.type];
	// execute each event handler
	for (var i in handlers) {
		this.$$handleEvent = handlers[i];
		if (this.$$handleEvent(event) === false) {
			returnValue = false;
		}
	}
	return returnValue;
};

function fixEvent(event) {
	// add W3C standard event methods
	event.preventDefault = fixEvent.preventDefault;
	event.stopPropagation = fixEvent.stopPropagation;
	return event;
};
fixEvent.preventDefault = function() {
	this.returnValue = false;
};
fixEvent.stopPropagation = function() {
	this.cancelBubble = true;
};
```

这段代码其实是对IE浏览器事件绑定的一个修补，特别是旧版本的（IE8及更早的版本）。jquery借鉴了这样的一个思路，写出了兼容各个浏览器的event模块。

## 3. jquery的实现思路

在《JavaScript忍者秘籍》中，给出了一个更加高级的实现，他使用一个中间事件处理程序，并将所有的处理程序都保存在一个单独的对象上，最大化地控制处理的过程，这样做有几个好处：

* 规范处理程序的上下文，这个指的是作用域的问题，正常来说，元素的事件处理程序的上下文应该就是元素本身，即`this === el`为true。
* 修复Event对象的属性，通过兼容性的处理，来达到与标准无异。
* 处理垃圾回收
* 过滤触发或删除一些处理程序
* 解绑特定类型的所有事件
* 克隆事件处理程序

依照这样的一个思路，我们来一步步实现这样一个模块。

### 3.1 修复Event对象的属性

修复主要针对一些重要的属性进行修复，结合[上一节的内容](./event.md)，有以下代码：

```javascript
function fixEvent(event) {
    function returnTrue () {return true;}
    function returnFalse () {return false;}

    if(!event || !event.stopPropagation) { // 判断是否需要修复
        var old = event || window.event; // IE的event从window对象中获取

        event = {}; // 复制原有的event对象的属性

        for(var prop in old) {
            event[prop] = old[prop];
        }

        // 处理target
        if(!event.target) {
            event.target = event.srcElement || document;
        }

        // 处理relatedTarget
        event.relatedTarget = event.fromElement === event.target ? 
                                        event.toElement : 
                                        event.fromElement;
        
        // 处理preventDefault
        event.preventDefault = function () {
            event.returnValue = false;
            // 标识，event对象是否调用了preventDefault函数
            event.isDefaultPrevented = returnTrue; 
        }
        /*
            可以调用event.isDefaultPrevented()来查看是否调用event.preventDefault
        */
        event.isDefaultPrevented = returnFalse; 

        event.stopPropagation = function () {
            event.cancelBubble = true;
            event.isPropagationStopped = returnTrue;
        }

        event.isPropagationStopped = returnFalse;

        // 阻止事件冒泡，并且阻止执行其他的事件处理程序
        // 借助标识位，可以在后面进行handlers队列处理的时候使用
        event.stopImmediatePropagation = function () {
            event.isImmediatePropagationStopped = returnTrue;
            event.stopPropagation();
        }

        event.isImmediatePropagationStopped = returnFalse;

        // 鼠标坐标，返回文档坐标
        if(event.clientX != null){
            var doc = document.documentElement, body = document.body;

            event.pageX = event.clientX +
                (doc && doc.scrollLeft || body && body.scrollLeft || 0) - 
                (doc && doc.clientLeft || body && body.clientLeft || 0);
            
            event.pageY = event.clientY +
                (doc && doc.scrollTop || body && body.scrollTop || 0) -
                (doc && doc.clientTop || body && body.clientTop || 0);
        }
    
        event.which = event.charCode || event.keyCode;

        // 鼠标点击模式 left -> 0 middle -> 1 right -> 2
        if(event.button != null){
            event.button = (event.button & 1 ? 0 : 
                    (event.button & 4 ? 1 : 
                        (event.button & 2 ? 2 : 0)));
        }
    }
    return event;
}
```

### 3.2 中央对象保存dom元素信息

这个的目的是为了给元素建立一个映射，标识元素和存储相关联的信息(事件类型和对应的事件处理程序)，在jquery里面使用的是selector，在《JavaScript忍者秘籍》中，使用的是guid。

```javascript
var cache = {},
    guidCounter = 1,
    expando = "data" + (new Date).getTime();

function getData(el) {
    var guid = el[expando];
    if(!guid){
        guid = el[expando] = guidCounter++;
        cache[guid] = {};
    }
    return cache[guid];
}

function removeData(el) {
    var guid = el[expando];
    if(!guid) return;
    delete cache[guid];
    try {
        delete el[expando];
    } catch(e){
        if(el.removeAttribute){
            el.removeAttribute(expando);
        }
    }
}
```

### 3.3 绑定事件处理程序

```javascript
var nextGuid = 1;

function addEvent(el, type, fn) {
    
    var data = getData(el);

    if(!data.handlers)data.handlers = {};

    if(!data.handlers[type])data.handlers[type] = [];

    // 给事件处理程序赋予guid，便于后面删除
    if(!fn.guid)fn.guid = nextGuid++;

    data.handlers[type].push(fn);

    // 为该元素的事件绑定统一的回调处理程序
    if(!data.dispatcher) {
        // 是否启用data.dispatcher
        data.disabled = false;
        data.dispatcher = function (event) {
            if(data.disabled)return;
            event = fixEvent(event);
            var handlers = data.handlers[event.type];
            if(handlers) {
                for(var i = 0, len = handlers.length; i < len; i++){
                    handlers[i].call(el, event);
                }
            }
        };
    }

    // 将统一的回调处理程序注册到，仅在第一次注册的时候需要
    if(data.handlers.length === 1){
        if(el.addEventListener){
            el.addEventListener(type, data.dispatcher, false);
        } else (el.attachEvent) {
            el.attachEvent("on" + type, data.dispatcher);
        }
    }
}
```

### 3.4 清理资源

绑定了事件，就还需要一个解绑事件，因为我们使用的是委托处理程序来控制处理流程，而不是直接绑定处理程序，所以也不能直接使用浏览器提供的解绑函数来处理。在这里，我们需要手动来清理一些资源，清理的顺序从小到大。

```javascript
function isEmpty(o){
    for(var prop in o){
        return false;
    }
    return true;
}

function tidyUp(el, type) {
    var data = getData(el);

    // 清理el的type事件的回调程序
    if(data.handlers[type].length === 0) {
        delete data.handlers[type];

        if(el.removeEventListener){
            el.removeEventListener(type, data.dispatcher, false);
        } else if(el.detachEvent){
            el.detachEvent("on" + type, data.dispatcher);
        }
    }

    // 判断是否还有其他类型的事件处理程序，如果没有则进一步清除
    if(isEmpty(data.handlers)){
        delete data.handlers;
        delete data.dispatcher;
    }

    // 判断是否还需要data对象
    if(isEmpty(data)) {
        removeData(el);
    }
}
```

### 3.5 解绑事件处理程序

为了尽可能保持灵活，提供了以下的功能

* 将一个元素的所有绑定事件进行解绑

    ```javascript
    removeEvent(el);
    ```

* 将一个元素特定类型的所有事件进行解绑

    ```javascript
    removeEvent(el, "click");
    ```

* 将一个元素的特定处理程序进行解绑

    ```javascript
    removeEvent(el, "click", handler);
    ```

```javascript
function removeEvent(el, type, fn) {
    var data = getData(el);

    if(!data.handlers)return;

    var removeType = function(t) {
        data.handlers[t] = [];
        tidyUp(el, t);
    };

    // 删除所有的处理程序
    if(!type){
        for(var t in data.handlers){
            removeType(t);
        }
        return;
    }

    var handlers = data.handlers[type];
    if(!handlers)return;

    // 删除特定类型的所有事件处理程序
    if(!fn){
        removeType(type);
        return;
    }

    // 删除特定的事件处理程序，这个时候根据guid来进行删除
    // 这里需要考虑的就是可能一个事件处理程序被绑定到一个事件类型多次
    // 因此，这里需要用到handlers.length，删除的时候，需要n--
    if(fn.guid) {
        for(var n = 0; n < handlers.length; n++){ 
            if(handlers[n].guid === fn.guid){
                handlers.splice(n--, 1);
            }
        }
    }

    // 返回之前进行资源清理
    tidyUp(el, type);
}
```

到这里，我们就得到一个既保证通用性又保证性能的事件监听处理模块，然而事件的知识并不仅仅这么一点，本章节的内容将会继续出现在接下来的几个小节，一起构建一个完整的event体系的代码库。

## 4. 参考

1. 《JavaScript高级程序设计》
2. 《JavaScript忍者秘籍》
3. [addEvent](http://dean.edwards.name/weblog/2005/10/add-event/)