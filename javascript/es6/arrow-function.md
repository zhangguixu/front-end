# Arrow Functions(箭头函数)

为了保证this的指向，我们通常需要

```javascript
var logUpperCase = function() {
    var _this = this; //that = this,_self =this
    this.string = this.string.toUpperCase();

    return function() {
        return console.log(_this.string);
    }
}
```

在ES6中，仍然保留以前的function的传递方式，但是引进了`箭头函数`，可以保证this的指向。

```javascript
var logUpperCase = function() {
    this.string = this.string.toUpperCase();
    return () => console.log(this.string);
}

logUpperCase.call({string:'ES6 rocks'})();
```

*以后再详细学习各种写法*