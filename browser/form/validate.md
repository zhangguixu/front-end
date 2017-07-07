# 表单校验

## 1. 概述

表单校验一般有几种情况

1. 非空校验
2. 长度校验
3. 格式校验，常见的有手机号码，邮箱
4. 密码前后一致校验
5. 密码强度校验

## 2. 表单自带属性校验

### 2.1 非空校验[H5]

在某输入型控件设置`require`属性，那么此项必填，否则就无法提交表单

```html
<input type="text" required>
```

### 2.2 范围校验[H5]

在控件range和number中，都可以通过指定`max`和`min`两个属性来限定输入数值的范围。

```html
<input type="number" max="10" min="1">
```

### 2.3 长度校验

文本输入的时候，可以通过`maxLength`限定输入的长度。

```html
<input type="text" maxLength="140">
```

### 2.4 pattern校验[h5]

通过`pattern`属性来指定输入所需要满足的正则表达式，目前暂不建议的使用，支持的浏览器很少。

```html
<input type="number" pattern="[0-9]{16}">
```

需要输入16个数字，才能过校验。

### 2.5 格式验证[H5]

在表单字段类型中，可以将type属性设置为`email`或`url`，这些属性不仅能反映数据类型的信息，而且还提供了一些默认的验证功能，而这两个属性值是被支持最多的类型。

```html
<input type="email" name="email">
<input type="url" name="homepage">
```

要检测浏览器是否支持这些新类型，可以尝试对input元素的type属性进行赋值，再检测这个属性的值，不支持它们的旧版本浏览器会自动将未知的值设置为text，而支持的浏览器则会返回正确的值。

```javascript
function isTypeSupported (type) {
    var input = document.createElement("input");
    try {
        input.type = type;
    } catch(e){}

    return input.type == type;
}
```

## 3. HTML5的表单验证

### 3.1 检测有效性

使用`checkValidity()`可以检测表单中的某个字段是否有效。字段的值是否有效的判断依据是第二小节的介绍的那些约束，如果有效则返回true。

也可以对整个表单调用这个方法，如果所有表单字段都有效，这个方法返回true，反之如果有一个字段无效，这个方法会返回false。

```javascript
if(document.forms[0].checkValidity()){
    // 表单有效
} else {
    // 表单无效
}
```

### 3.2 validity属性

我们可以通过表单字段的`validity`属性检测表单的有效性，validity属性包含一系列属性，每个属性都会返回一个布尔值。

|validity的属性|说明|
|:--|:--|
|customError|如果设置了setCustomValidity()，则为true，否则为false|
|patternMismatch|如果值与指定的pattern属性不匹配，返回true|
|rangeOverflow|如果值比max值大，返回true|
|rangeUnderflow|如果值比min值小，返回true|
|stepMisMatch|如果min和max之间的步长不合理，返回true|
|tooLong|如果值的长度超过了maxLength属性指定的长度，返回true|
|typeMismatch|如果值不是mail或url要求的格式，返回true|
|valid|如果这里的其他属性都是false，返回true|
|valueMissing|如果标注为required的字段中没有值，返回true|

```javascript
if(input.validity && !input.validity.valid) {
    if(input.validity.valueMissing){
        console.log("值缺失");
    } else if(input.validity.typeMisMatch) {
        console.log("类型不匹配");
    } else {
        console.log("验证不通过");
    }
}
```

### 3.3 禁止验证

可以对表单设置`novalidate`属性，来告诉表单不进行验证

```html
<form method="post" action="" novalidate>
</form>
```

如果一个表单有多个提交按钮，可以为某个提交按钮设置`formnovalidate`属性使得这个提交按钮点击提交而不必验证表单。

```html
<form method="post" action="">
    <input type="submit" value="正常提交">
    <input type="submit" value="不验证提交" formnovalidate>
</form>
```

## 参考

1. 《HTML5程序设计》
2. 《JavaScript高级程序设计》