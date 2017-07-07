# 函数柯里化

## 1. 概念

函数柯里化（function currying），它用于创建已经设置好了一个或多个参数的函数。它是与函数绑定紧密相关的主题。

## 2. 创建柯里化函数

调用另一个函数，并为它传入要柯里化的函数和必要参数。

```javascript
function curry(fn){
    var args = Array.prototype.slice.call(arguments,1);
    return function(){
        var innerArgs = Array.prototype.slice.call(arguments);
        var finalArgs= args.concat(innerArgs);
        return fn.apply(null,finalArgs);
    }
};
```

示例：创建一个第一个参数绑定为3的add()的柯里化的版本

```javascript
function add(n1,n2){return n1 + n2;}
var curriedAdd = curry(add,3);
console.log(curriedAdd(5));//8
```

## 3. 柯里化与函数绑定的结合

函数柯里化常常作为函数绑定的一部分包含在其中，构造出更为复杂的bind()函数。

```javascript
function bind(fn,context){
    var args = Array.prototype.slice.call(arguments,1);
    return function(){
        var innerArgs = Array.prototype.slice.call(arguments);
        var finalArgs = args.concat(innerArgs);
        return fn.apply(context,finalArgs);
    }
}
```

这样的bind()函数不仅可以返回绑定给定环境的函数，并且可能绑定其中的某些函数参数。

## 4. 总结

1. ECMAScript5的bind()方法实现了函数柯里化。
2. 无论是柯里化函数或是绑定函数，都会带来额外的开销，所以不应滥用。

## 5. 参考

1. 《JavaScript高级程序设计》