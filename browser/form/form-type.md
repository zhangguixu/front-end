# 表单类型详述

表单的类型即type有很多种，特别是HTML5出来之后，又增加了很多类型来增强表单的功能。

## 1. 文本输入

|类型|说明|HTML5|
|:---|:--|:--|
|text|输入文本|X|
|password|输入密码|X|
|email|编辑email|√|
|tel|输入电话号码|√|
|url|输入url|√|
|search|搜索单行文本|√|

## 2. 数字控件

|类型|说明|HTML5|
|:--|:--|:--|
|number|用于输入浮点数的控件|√|
|range|用于输入不精确值控件|√|

## 3. 日期控件

|类型|说明|HTML5|
|:--|:--|:--|
|date|输入日期的控件（年月日）|√|
|datetime|基于UTC的日期时间输入控件|√|
|datatime-local|输入日期的时间控件，不包含时区|√|
|month|用于输入年月的控件，不带时区|√|
|time|用于输入不含时区的事件控件|√|
|week|用于输入一个由星期-年组成的日期，不包含时区|√|

## 4. 选择控件

### checkbox

复选框，有两种状态：选中和未选择中。获取这个值要先使用checked属性来指示控件是否被选择，再读取value获取该值。

```javascript
if(checkbox.checked){
    // 如果有勾选
    var value = checkbox.value;
}
```

### radio

单选按钮，在一个单选按钮组中（即它们的name相同），同一个时间只有一个单选按钮可以被选择，举个例子

```html
<!-- 一组单选按钮 -->
<input type="radio" name="test" value="1">1
<input type="radio" name="test" value="2">2
<input type="radio" name="test" value="3">3

<!-- 另外一组 -->
<input type="radio" name="test-1" value="test">test
```

获取单选按钮的值，必须遍历，检测它们的checked属性来判断是否被选中。

```javascript
var test = document.getElementsByName("test");
var testValue;
for(var i = 0, len = test.length; i < len; i++){
    if(test[i].checked){
        testValue = test[i].value;
        break;
    }
}
```

### select

选择框是通过\<select\>和\<option\>元素创建的。它有属于自己的属性和方法

* add(newOption, relOption):向控件中插入新的\<option\>，在相关项之前
* multiple: 布尔值，表示是否允许多项选择
* options: 控件中所有\<option\>元素
* remove(index): 移除给定位置的选项
* selectedIndex : 只返回选中的第一项索引，如果没有则值为-1
* size: 选择框中可见的行数

选择框的type属性有两种：

* 单列列表 \<select\>..\</select\> "select-one"
* 多选列表 \<select multiple\>..\</select\> "select-multiple"，这种可以多选，需要按ctrl+点击

选择框的值，遵循一下的规则

* 如果没有选中值，其value保存空字符串
* 如果有选中，则value值为对应的HTML的value属性(option.value)，如果是空字符串，也一样
* 如果有选中，而HTML没有value属性，则选择框的value等于该项的文本(option.text)
* 如果有多个选中项，则选择框的value值将依据前两条规则取得第一个选中项的值。

用法示例如下

```html
<select>
    <option value="1">1</option>
    <option value="2">2</option>
    <option value="3">3</option>
    <option value="4">4</option>
</select>
```

这个表单结构看起来比较复杂，但是获取它的值其实很简单

```javascript
var value = select.value;
```

不过应该注意的是，在大部分时候，可能第一个选项都是提示性信息而非真正的选择的值，因此要做一下判断。

## 5. 文件控件

提交文件的控件只有一个，当表单中有文件控件的时候，需要将表单的编码类型指定为`multipart/form-data`

```html
<input type="file" >
```

## 6. 特殊控件

|类型|说明|HTML5|
|:--|:--|:--|
|hidden|不显示在页面的控件，它的值会被提交到服务器|X|
|submit|用于提交表单的按钮|X|
|reset|用于重置表单的按钮|X|

## 7. 参考

1. [MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/Input)
2. [表单类型体验](http://www.w3schools.com/html/html_form_input_types.asp)