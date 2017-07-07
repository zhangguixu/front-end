# 类型检测

在js中提供两种检测类型的方法，但是它们并不总是靠谱。

1. typeof
2. instanceof

下面我们来看看各个类型的应该如何进行检测比较靠谱

## 1. 基本类型

基本类型(除null之外)的检测使用`typeof`，它的语法很简单

```javascript
var number = 1,
    string = "s",
    boolean = true,
    un = undefined,

typeof number; // "number"
typeof string; // "string"
typeof boolean;// "boolean"
typeof un; // "undefined"
```

### 1.2 null

如果我们使用typeof来检测，结果会是

```javascript
var n = null;
typeof n;//"object"
```

有人说这是个bug，有人说这是因为null本身就代表着一个空对象，我们也经常用null来解除引用。不管怎么说，我们可以这样检测null

```javascript
var n = null;
function isNull(n){
    if(!n && typeof n !== "undefined"){
        return true;
    }
    return false;
}
isNull(n);// true
```

### 1.3 非数字

判断一个变量是否是数字，可以使用isNaN()，这个方法的意思是`is not a numher`，也就是说返回true的时候说明变量不是一个数字。

```javascript
var a = 1,
    b = "s",
    c = "1";
isNaN(a); // false
isNaN(b); // true
isNaN(c); // true
```

## 2. 引用类型

引用类型使用typeof检测，返回都是"object"。这其实没什么意义，我们要知道的是变量具体是哪个引用类型的，例如数组，自定义的类型等等。这个时候可以用instanceof，它的语法是

`实例 instanceof 对象构造函数`

```javascript
var arr = [];
arr instanceof Array; // true
function Person(){};
var p = new Person();
p instanceof Person; // true
```

instanceof不仅能检测构造对象的构造器，还检测原型链。

```javascript
var now = new Date();
now instanceof Date; // true
now instanceof Object; // true
```

### 2.1 数组检测

数组在编程中相当常见，在框架中也是，如何准确地检测数组类型，是相当重要的。

可能有人会说了，使用instanceof不就可以了。非也。在js中，有一个很古老的跨域问题：在iframe之间来回传递数组，而instanceof不能跨帧。

什么叫不能跨帧？假设在一个浏览器的帧（frame A）里的一个对象传入到另外一个帧（frame B）中，两个帧都定义了一个构造函数Person，如果来自帧A的对象是帧APerson的实例，则

* frameAPersonInstance instanceof frameAPerson; //true
* frameAPersonInstance instanceof frameBPerson; //false

那怎么办？

首先，有人提出了使用`duck typing`的思路来解决这个问题，`duck typing`的意思就是：像鸭子一样走路、游泳并且嘎嘎叫的就是鸭子，这是个很有意思的想法，它的本质就是我们关注对象能做什么，不是关注对象是什么，采用这样的思路，我们检测对象实例是否有sort属性来判断对象实例是否是一个数组，具体实现如下

```javascript
function isArray(value) {
    return typeof value.sort === "function";
}
```

之后，kangax发现了一个很有趣的现象，调用某个值的内置toString()方法在所有浏览器中都返回标准的字符串结果，对于数组来说，返回的字符串为"[object Array]"，这个方法对识别内置对象都非常有效，因此jq也是采取了这种方式来实现的isArray()的方法。

```javascript
function isArray(value) {
    return Object.prototype.toString.call(value) === "[object Array]";
}
```

之后，ECMA5将Array.isArray()正式引入JavaScript，提供了一个能准确检测一个变量是否为数组类型的

```javascript
Array.isArray(variable);
```

## 3. 最后

到这里，你以为已经解决了所有的类型检测问题了吗？并不是。在《JavaScript框架设计》这本书中，详细地谈论各种类型检测的兼容性问题，并且在`massframework`给出了一个很不错的实现，从代码里面来学习可以学到更多。

```javascript
// 建立类型的映射
var class2type = {
    "[objectHTMLDocument]"       : "Document",
    "[objectHTMLCollection]"     : "NodeList",
    "[objectStaticNodeList]"     : "NodeList",
    "[objectIXMLDOMNodeList]"    : "NodeList",
    "[objectDOMWindow]"          : "Window",
    "[object global]"            : "Window", //safari 4.04
    "null"                       : "null",
    "NaN"                        : "NaN",
    "undefined"                  : "undefined"
};

// 正常情况下的类型
"Boolean,Number,String,Function,Array,Date,RegExp,Window,Document,Arguments,NodeList"
    .replace(/[^, ]/g, function(name) {
        class2type['[object ' + name + ']'] = name;
    });

var toString = class2type.toString;

mass.type = function(obj,str){
	var result = class2type[ (obj == null || obj !== obj) ? obj : toString.call(obj) ]
					|| obj.nodeName || '#';

	if(result.charAt(0) === '#' ){  //兼容旧版浏览器与处理个别情况，如window.opera

		//hack,利用IE6,IE7,IE8 window == document 为 true，document == window 为 false的特性
		if(obj == obj.document && obj.document != obj){
			result = 'Window';
		} else if (obj.nodeType === 9){ //nodeType 属性返回以数字值返回指定节点的节点类型。9代表document
			result = 'Document';
		} else if (obj.callee){ //arguments 特有的属性
			resutl = 'Arguments';
		} else if (isFinite(obj.length) && obj.item){ //obj.item()方法，可返回节点列表中处于指定的索引号的节点。
			result = 'NodeList';
		} else {
			result = toString.call(obj).slice(8,-1); //[object XXXX]
		}

	}

    //提供一个进行类型比较的功能
	if(str){ 
		return str === result;
	}
	return result;
}
```

## 4. 参考

1. 《编写可维护的JavaScript》
2. 《JavaScript框架设计》


