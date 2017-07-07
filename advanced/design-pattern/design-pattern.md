# 常用的设计模式

## 1. 桥接模式

### 作用

桥接模式的作用在于将实现部分和抽象部分分离开来，以便两者可以独立的变化，在实现API的时候，桥接模式特别有用。

### 例子

forEach函数的实现，用来迭代一个数组。

```javascript
forEach = function (arr, fn) {
    for (var i = 0, l = arr.length; i < l; i++){
        var c = arr[i];
        if(fn.call(c, i, c) === false){
            return false;
        }
    }
}
```

可以看到，forEach函数并不关心fn里面的具体实现，fn里面的逻辑也不会被forEach函数改写影响。

## 2. 门面模式

### 概述

1. 门面模式（包装器）为一个已存在的对象创建一个新的接口，门面是一个全新的对象，其背后有一个已存在的对象。

2. 门面实现一个特定的接口，让一个对象看上去像另外一个对象，就称作一个适配器

3. jQuery和YUI的DOM接口都使用了门面，无法从DOM对象上继承，所以唯一能够安全地为其新增功能的选择就是创建一个门面

### 示例

```javascript
function DOMWrapper(element){
    this.element=element;
}

DOMWrapper.prototype.addClass= function(className){
    element.className+=' '+className;
};

DOMWrapper.prototype.remove=function(){
    this.element.parentNode.removeChild(this.element);
};


//用法示例
var wrapper=new DOMWrapper(document.getElementById('my-div'));

//添加一个class
wrapper.addClass('selected');

//删除元素
wrapper.remove();
```

## 3. 代理模式

### 定义

代理模式的定义是把一个对象的访问，交给另一个代理对象来操作。

### 场景

在实际的编程中，这种因为性能问题使用代理模式的机会是非常多的。比如频繁的访问dom节点，频繁地请求远程资源，可以把操作先存到一个缓冲区，然后自己选择真正的触发时机。

## 4. 观察者模式

### 认识

基于`事件`的编程，能够使得应用架构充分解耦，让功能变得更加内聚，且具有更好的可维护性，这种模式成为发布/订阅模式

### 实现

只需记录回调和事件名称的对应关系及调用它们的方法

```javascript
var PubSub={
    subscribe:function(ev,callback){
        //创建_callbacks对象
        var calls=this._callbacks||(this._callbacks={});

        //针对给定的key创建一个数组，除非这个数组已经存在
        //然后将回调函数追加到这个数组中
        (this._callbacks[ev]||(this._callbacks[ev]=[])).push(callback);
        return this;  //链式调用
    },
    publish:function(){
        //将arguments转为真正的数组（性能要求！apply参数需求）
        var args=Array.prototype.slice.call(arguments,0);

        //拿出第1个参数，即事件名称
        var ev=args.shift();

        //如果不存在_callback对象，则返回
        //或者如果不包含给定事件对应的数组
        var list,calls,i,l;

        //优先使用局部变量，提高性能
        if(!(calls=this._callbacks))return this;

        if(!(list=this._callbacks[ev]))return this;

        //触发回调
        for(i=0,l=list.length;i<l;i++)
            list[i].apply(this,args);

        return this;
    }
}
```
## 5. 单例模式

### 定义

单例模式的定义是产生一个类的唯一实例。

*javscript是面向对象的语言，没有类的概念*

很多文章把`{}`当成一个单例，因为没有构造函数，无法再生成跟上面一样的对象实例了。然而这并没有什么意义。

### 场景分析

设想这样的一个场景，我们在点击某个按钮的时候需要在页面弹出一个遮罩层，这个遮罩层是全局唯一的，即我们需要一个单例来保存这样的一个遮罩层实例。

```javascript
var createMask = function () {
    var mask;
    return function () {
        return mask || (document.createElement("div"));
    }
}
```

*此处里面闭包来进行封装，为的是生成的单例不被修改*

### 实现

我们来实现一个通用的`singleton`包装器

```javascript
var createSingleton = function (factory) {
    var singleton;
    return function () {
        return singleton || (factory.apply(this,arguments));
    }
}
```

## 6. 策略模式

### 定义

策略模式是定义一系列的算法，把它们一个个封装起来，并且使它们可相互替换。

### 例子

jQuery的animate方法

```javascript
$(div).animate({"left":"200px"}, 1000, "linear"); //匀速运动
$(div).animate({"left":"200px"}, 1000, "cubic"); //三次方的缓动
```

在这里，linear和cubic就是一种策略模式的封装，通过参数的不同来调用不同的算法



