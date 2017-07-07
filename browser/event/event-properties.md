# event对象

当一个事件触发时，就会产生一个event对象，然后传递到事件处理程序中，这个对象会携带事件触发时的信息。

本节主要谈论event对象中的一些重要的属性以及这些属性在IE和其他浏览器中的区别。

*本文的IE都是指IE9之前的版本*

## 重要属性

### 1. target

表示事件原始源的属性。在IE中，原始源保存在`srcElement`中

### 2. relatedTarget

表示事件触发时的关联属性（mouseover或mouseout），在IE中则是`toElement`和`fromElement`

### 3. preventDefault

表示阻止默认浏览器行为的发生。该属性在IE中不存在的，要达到一样的效果需要将`returnValue`置为false。

### 4. stopPropagation

表示阻止事件冒泡。在IE中，不存在该属性，要达到一样的效果需要将`cancelBubble`置为true。

### 5. pageX & pageY

提供了鼠标相对于整个文档的位置。在IE中不存在，我们需要通过

* clientX/Y : 提供鼠标相对于窗口的位置
* scrollTop/Left : 提供文档滚动的位置
* clientTop/Left : 提供文档的偏移量

这三个属性来计算出最后的pageX/Y的值。

### 6. which

表示键盘事件时所按键的键盘码。在IE中，可以通过`charCode`和`keyCode`属性获取到。

### 7. button

表示鼠标事件发生时，用户单击的鼠标按键。在IE中使用了位掩码

* 左单击是1
* 中间单击是4(即滚轮)
* 右击是2

需要将其转换为标准的等价值0，1，2。

## 来源

1. 《JavaScript忍者秘籍》
2. [Event对象的兼容性](http://www.quirksmode.org/dom/w3c_events.html)
