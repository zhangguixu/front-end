# 增强的表单元素

## placeholder

提示表单填写信息

```html
<input name="name" type="text" placeholder="姓名">
```

## autocomplete

用来保护敏感数据，避免本地浏览器对它们进行不安全的存储，可以取三个值

* on: 该字段无需受到保护，值可以被保存和恢复
* off: 该字段收到保护，值不可以被保存
* unspecified: form的默认设置

```html
<input type="text" autocomplete="off">
```

## autofocus

页面载入时，通过autofocus的特性可以指定某个表单获得输入焦点，每个页面只允许出现一个autofocus。

```html
<input type="search" autofocus>
```

## spellcheck

可以对带有文本内容的输入空间和textarea控件设置spellcheck属性，设置完成后，它会询问浏览器是否应该给出拼写检查结果反馈，大部分浏览器都默认启用了spellcheck。

```html
<textarea spellcheck="true"></textarea>
```

## list & datalist

通过组合使用list和datalist元素，开发人员能够为某个输入空间构建一张选值列表。

1. 创建id值唯一的datalist元素，该元素可以插入文档任意位置。

    ```html
    <datalist id="contractList">
        <option value="1">1</option>
        <option value="2">2</option>
        <option value="3">3</option>
    </datalist>
    ```

2. 添加若干option元素作为datalist的子元素，表示某控件推荐选择的全集

    ```html
    <input type="email" list="contractList">
    ```

## range控件

range控件可以设置四个属性

* min: 数值输入的最低值
* max: 数值输入的最高值
* step: 指定输入值递增或递减的粒度
* valueAsNumber: 完成控件值类型在文本与数值间的相互转换，既是setter函数，也是getter函数，可用于为控件设置number值，输入的数值必须同时满足min、max和step

```html
<input type="range" min="0" max="100" value="3" step="5">
```

```javascript
document.getElementById('range').valueAsNumber(65);
```

## required

一旦为输入型控件设置了required，那么此项必填，否则无法提交表单

```html
<input type="text" required>
```
