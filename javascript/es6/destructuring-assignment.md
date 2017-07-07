# Destructuring Assignment(解构赋值)

对象属性的赋值，通常的做法是

```javascript
var data = $('body').data(), //data has properties house and mouse
    house = data.house,
    mouse = data.mouse;
```

在ES6中，可以这样写

```javascript
var {house,mouse} = $('body').data();
```

对于数组也可以有这种便利的写法

```javascript
var a = [1,2];
var [v1,v2] = a;
```
