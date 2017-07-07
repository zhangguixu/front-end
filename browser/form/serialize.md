# 表单序列化

## 1. 概述

随着Ajax的出现，表单的序列化已经是一个常见的需求。我们先明确表单提交时，浏览器是怎么样将数据发送给服务器的

* 对表单字段的名称和值进行URL编码（调用encodeURIComponent()方法），使用`&`分隔，无论是get还是post请求，都是一样的
* 不发送禁用的表单字段，即属性`disabled`为true的
* 只发送勾选的复选框和单选按钮
* 不发送type为reset和button的按钮
* 多选选择框中每个选择的值单独一个条目
* 在单击提交按钮表单的情况下，也会发送提交按钮的value值，否则不发送提交按钮。
* select元素的值，就是选中的option元素的value属性的值，如果option元素没有value属性，则是option元素的文本值。

## 2. 实现

跟据上述，我们可以来实现一个表单的序列化，代码是对《JavaScript高级程序设计》表单脚本一章中的代码改进

1. 增加参数验证
2. 将form.elements转为数组，提高性能
3. 改进多选选择框，同一个参数名的值进行合并,试验了一下，如果options没有HTML的value属性，读取该option的value属性会等于它的text属性(测试浏览器 IE8，chrome)

```javascript
function serialize(form) {
    // 参数验证
    if(!form || form.nodeName.toUpperCase() != "FORM" ){
        throw new Error("invalid parameters");
    }
    
    var encode = window.encodeURIComponent,
        slice = Array.prototype.slice;

    var params = [],
        i = 0,
        fields = slice.call(form.elements),
        len = fields.length,
        el,
        type;

    for(;i < len; i++){
        el = fields[i];
        type = el.type.toLowerCase();
        switch(type){
            case  "undefined":
            case  "button":
            case  "submit":
            case  "reset":
            case "file":
                break;
            case "select-one":
            case "select-multiple":
                if(el.name.length){
                    var j = 0, 
                        opts = slice.call(el.options),
                        oLen = opts.length,
                        selected = [] , // 用于合并参数
                        opt;            
                    for(; j < oLen; j++){
                        opt = opts[j];
                        if(!opt.selected)continue;
                        selected.push(opt.value);
                    }
                    params.push(encode(el.name) + "=" + encode(selected.join(",")));
                }
                break;
            case "checkbox":
            case "radio":
                if(!el.checked){
                    break;
                }   
            default:
                if(el.name.length){
                    params.push(encode(el.name) + "=" + encode(el.value));
                }
        }
    }
    return params.join("&");
};
```

*以上代码在IE8会报错，原因是option元素的实现并非一个JavaScript对象，导致无法使用slice，当然解决的方法也有，就是重写slice函数，但是这脱离了本章节的主题。*

## 参考

1. 《JavaScript高级程序设计》