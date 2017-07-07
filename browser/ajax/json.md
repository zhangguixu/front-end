# json

## 1. 概述

JSON是一种用于网络通信的轻量级的数据格式，它比XML更加简洁。JSON是JavaScript的一个严格的子集，但是JSON并不从属于JavaScript。

在ECMASCript 5对解析JSON的行为进行规范，定义了全局对象JSON，这个对象有两个方法

* stringify(): 把一个JavaScript对象序列化为一个JSON字符串
* parse(): 将一个JSON字符串解析为原生的JavaScript值

## 2. 解析与序列化

### 2.1 eval()

早期一般都是采用eval()来进行JSON解析，但是在旧版本的浏览器中，使用eval()对JSON数据结构求值存在风险，因为可能会执行一些恶意的代码。

### 2.2 stringify()

stringify()可以接收另外两个参数，可以用于以不同的方式序列化JavaScript对象。

1. 第一个参数是过滤器，可以是一个数组，也可以是一个函数

    可以通过数组来指定序列化的属性

    ```javascript
    JSON.stringify(book, ["title","edition"]);
    ```

    通过函数也可以进行过滤

    ```javascript
    JSON.stringify(book, function(key, value){
        switch(key){
            // 进行处理
        }
    });
    ```

2. 第二个参数是一个选项，表示是否在JSON字符串中保留缩进

    例如在每个级别缩进4个空格

    ```javascript
    JSON.stringify(book, null, 4);
    ```

### 2.3 toJSON()

在有些情况下，可以通过对象上调用toJSON()方法，返回其自身的JSON数据格式，也可以为任何对象添加toJSON()方法。

例如Date对象就有一个toJSON方法，能够将JavaScript的Date对象自动转换成ISO日期字符串。

### 2.4 parse()

parse()还可以再接收一个函数作为参数，对每个键值进行过滤（又叫做还原）。在函数中返回undefined，则表示从结果中删除响应的键，如果返回其他值，则将该值插入到结果中。

例如将日期字符串转换为Date对象时，经常要用到

```javascript
JSON.parse(jsonText, function(key, value){
    if(key == "releaseDate") {
        return new Date(value);
    } else {
        return value;
    }
});
```