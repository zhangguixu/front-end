# Default Parameters(默认参数)

参数需要进行非空校验，选定默认值，一般使用`||`

```javascript
var link = function (height,color,url) {
    var height = height || 50;
    var color = color || 'red';
    var url = url || 'qq.com';
}
```

在ES6，可以直接把默认值放在函数申明中

```javascript
var link = function(height = 50,color = 'red',url='qq.com'){
    //
}
```

*类Ruby的语法*