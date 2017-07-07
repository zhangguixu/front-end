# document

document对象代表了当面的页面文档，通过这个对象的属性，我们可以当前页面进行操作。

## 属性

有用的属性值

|值|说明|
|:--|:--|
|document.cookie|页面的cookie|
|document.domain|页面的域名，修改会受到一定的限制|
|document.location|页面的url信息，是一个对象|
|document.url|页面的url，字符串|
|docuemnt.readyState|页面的加载情况|
|document.onreadyStateChange|加载情况改变的回调函数|
|document.title|页面的标题|
|document.compatMode|页面的渲染模式，quirks或strict|
|document.documentElement|对于HTML文档来说，返回的一般是HTML元素|
|document.hidden|文档不可见|
|document.visibilityState|文件的可见的状态|

快捷访问

|值|说明|
|:--|:--|
|document.body|body|
|document.activeElement|返回当前聚焦的元素|
|document.images|img标签|
|document.head|head标签|
|document.forms|form标签|

## 方法

### 1. 创建

1. document.createElement(tagname) - 创建一个元素
2. document.createDocumentFragment() - 创建一个文档片段
3. document.createTextNode(text) - 创建一个文本节点
4. document.createEvent(type) - 创建一个事件

### 2. 选取元素

1. document.getElementById(id)
2. document.getElementsByTagName(tagname)
3. document.getElementsByName(name)
4. document.getElementsByClassName(className)
5. document.querySelector(selector)
6. document.querySelectorAll(selector)
7. document.elementFromPoint(x, y)

### 3. 输出字符串

1. document.write(text)
2. document.writeIn(text)