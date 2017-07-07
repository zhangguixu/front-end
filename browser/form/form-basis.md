# 表单基础知识

JavaScript最初的一个应用，就是分担服务器处理表单的责任，打破处处依赖服务器的局面。

如今表单依旧在日常开发扮演着重要的角色，HTML5也对表单有所增强，来减轻开发人员对表单操作的负担。

## 1. form

表单是有\<form\>元素来表示，它有几个常用的属性

|属性|说明|html中是否有|
|:--|:--|:--|
|action|接受请求的url|√|
|enctype|请求的编码类型|√|
|method|HTTP请求类型|√|
|name|表单的名称|√|
|acceptCharset|服务器能够处理的字符集|√(accept-charset)|
|target|用于发送请求和接收响应的窗口名称|√|
|reset()|将所有表单域重置为默认值|type=reset|
|submit()|提交表单|type=submit|
|elements|表单中所有控件的集合(HTMLCollection)|X|
|length|表单中控件的数量|X|

访问表单可以通过id，也可以通过快捷访问`document.forms`

表单的`enctype`常用的有两种

* appliaction/x-www-form-urlencoded 在发送前编码所有字符（默认）
* multipart/form-data 不对字符编码，在包含文件上传控件的表单必须包含

## 2. 表单基本操作

表单提供了两种操作，从属性上看就知道是：

1. 提交
2. 重置

### 2.1 提交

提交表单有两种方式：

1、 通过提价按钮的方式

我们可以通过将其type属性设置为`submit`的方式，就可以得到一个提交按钮，用户单机提交按钮，就会提交表单（默认行为）。

```html
<!-- 通用按钮 -->
<input type="submit" value="提交按钮">

<!-- 自定义提交按钮 -->
<button type="submit">提交按钮</button>

<!-- 图像按钮 -->
<input type="image" src="graphic.gif">
```

在有提交按钮的情况下，在相对应的表单空间拥有焦点的情况下，按回车就可以提交该表单（textarea是例外），没有则不会。

此外，以这种方式提交表单，浏览器会在发送请求之前触发一个`submit`事件，这样我们就有机会验证表单数据，来决定是否允许表单提交。阻止这个事件的默认行为或者返回false都可以阻止表单提交。

```javascript
var form = document.getElementById("form");
form.addEventListener("submit", function(e){
    e.preventDefault();

    // return false;
}, false)
```

2、通过调用submit()方法的方式

```javascript
form.submit();
```

以这种形式提交表单，不会触发submit事件，因此要记得在调用此方法之前先验证表单数据。

### 2.2 重置表单

类似的，重置表单也有两种方式

1、 通过重置按钮

通过设置type属性为`reset`的方式就可以创建重置按钮，然后单击重置按钮就可以了。

```html
<!-- 通用重置按钮 -->
<input type="reset" value="重置按钮">

<!-- 自定义重置按钮 -->
<button type="reset">重置按钮</button>
```

同样单击之后，也会触发reset事件，

```javascript
form.addEventListener("reset", function(e){
    // 做些处理
}, false);
```

2、 调用reset()方法

同样可以通过JavaScript重置表单

```javascript
form.reset();
```

在调用reset()的时候，会像单击重置按钮一样触发reset事件。

## 3. 访问表单控件

我们可以通过表单的`elements`属性，来访问表单中的所有元素。`elements`集合是一个有序列表，每个表单字段在elements集合中的顺序，与它们出现在标记中的顺序相同，可以按照位置和name特性来访问它们。

例如：

```html
<form method="post" id="myForm">
    <ul>
        <li><input type="radio" name="id" value="1">1</li>
        <li><input type="radio" name="id" value="2">2</li>
        <li><input type="radio" name="id" value="3">3</li>
    </ul>
</form>
```

我们通过elements来访问它们

```javascript
var form = document.getElementById("myForm");
var ids = form.elements["id"]; // NodeList
var first = form.elements[0]; // 返回第一个元素
```

## 4. 共有的表单字段属性

表单字段有一些共同的属性，不同的类型的表单字段也有一些只属于自己的属性。

|属性|说明|
|:--|:--|
|name|当前字段的名称|
|value|当前字段提交给服务器的值（file只读）|
|type|当前字段的类型|
|form|指向当前字段所属表单的指针，只读|
|tabIndex|表示当前字段的切换序号|
|readOnly|布尔值，表示当前字段是否只读|
|disabled|布尔值，表示当前字段是否被禁用|

这些字段（除了form）在一般情况下，都可以通过JavaScript动态修改。

```javascript
var form = document.getElementById("form");
var field = form.elements[0];

// 修改value
field.value = "1";

// 检测form属性的值
console.log(field.form === form); // true

// 禁止当前字段
fiel.disabled = true;

// 修改type，不推荐，
field.type = "checkbox";
```

## 5. 共有的表单字段方法

每个表单字段都有两个方法

* focus()
* blur()

1、 获得焦点

focus()方法用于将浏览器的焦点设置到表单字段，即激活表单字段，使其响应键盘事件。例如接收到焦点的文本框会显示插入符号。

可以在页面加载之后，让表单的第一个字段获取焦点，这样来提高用户的体验

```javascript
window.addEventListener("load", function(e){
    document.forms[0].elements[0].focus();
}, false);
```

要注意的是，如果一个表单字段是一个input元素，且其`type=hidden`或者该字段是隐藏着的，那么对该表单字段调用focus()就会报错。

在HTML5中，为表单字段新增了一个autofocus属性，只要设置这个属性，就会把焦点自动移动相应的字段，一个页面只能有一个表单字段设置这个属性。在支持这个属性的浏览器中它的值应该是true。

```html
<input type="text" autofocus>
```

2、 移走焦点

仅仅将焦点从调用这个方法的元素上面移走而已。在早期的开发中国，由于表单字段还没有`readOnly`属性，因此就可以使用blur()方法来创建只读字段。

## 6. 共有的表单字段事件

除了支持一般的HTML事件，所有表单字段都支持下列3个事件

* blur: 当前字段失去焦点时触发
* focus: 当前字段获得焦点时触发
* change: 对于\<input\>和\<textarea\>元素，在它们失去焦点且value值改变时触发，对于\<select\>元素，在其选项改变时触发。

> 在某些浏览器中，blur事件会先于change事件发生；而在其他浏览器中恰好相反，为此，不能假定这两个事件以某种顺序依次触发。

## 5. 参考

1. 《JavaScript高级程序设计》