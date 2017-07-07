# 高级定时器

## 1. 基础知识

### 1.1 理解定时器

要理解setTimeout()和setInterval()，必须结合[事件循环机制](../core/event-loop.md)，简单来说，这两个指定的时间只是触发定时器事件的时间，然后回调函数会被放到任务队列中，等待主执行栈的任务执行完成之后，才会调用回调函数。

## 1.2 重复的定时器

setInterval有两个问题：

1. 某些间隔会被跳过
2. 多个定时器的代码执行之间的间隔可能会比预期的小

为了避免setInterval()的重复定时器的2个缺点，可以使用链式的setTimeout()调用

```javascript
setTimout(function(){
    //处理中

    setTimeout(arguments.callee,interval);

},interval);
```

这种写法可以保证在下一次定时器代码执行之前，至少要等到指定的间隔，避免了连续的运行。

### 1.3 创建异步任务

定级器一个高级的运用就是将一个同步的代码变成异步的。在很多时候我们在初始化页面时，为了加快页面的展示，就必须避免一些繁重的任务阻塞到UI线程，因此可以利用定时器来创建一个异步执行的代码。

```javascript
function lookup(){
    // 查询任务，可能会阻塞UI线程
}
var timer;
timer && (timer = setTimeout(lookup, 0));
```

## 2. 进阶知识

此外，定时器还有几点特别需要注意的

1. 定时器存在一个最小的时间间隔，最小的间隔大概在4ms左右，在旧版本的IE中，时间间隔会比较长（15.6ms左右）。
2. 间隔的时间也很容易受到外部因素影响，比如电池快没电，或者同时打开的应用程序太多了。

### 2.1 改造setTimeout

如果嫌旧版本IE的最短时钟间隔太大，可以利用image死链时立即执行onerror回调的情况下进行改造

```javascript
var orig_setTimeout = window.setTimeout;
window.setTimeout = function (fun, wait){
    if(wait > 15){
        orig_setTimeout(fun, wait);
    } else {
        var img = new Image();
        img.onload = img.onerror = function(){
            fun();
        }
        img.src = "data:,error";
    }
}
```

### 2.2 异步编程

浏览器环境与后端nodejs存在各种消耗巨大或阻塞线程的行为，对于JavaScript这样单线程的东西唯一解耦的方法就是提供异步API。待续。。。

## 参考

1. 《JavaScript高级程序设计》
2. 《JavaScript框架设计》