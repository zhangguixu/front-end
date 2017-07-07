# 函数作用域绑定

这个技巧常常和回调函数与事件处理一起使用，以便在将函数作为变量传递的同时保留代码执行环境。

很多JavaScript库实现了一个可以将函数绑定到指定环境的函数，这个函数一般都叫做bind()。一个简单的bind()函数接受一个函数和一个环境，并返回一个给的环境中调用给定函数的函数，并且将所有参数原封不动传递过去。这个函数返回的是一个`闭包`。

```javascript
function bind(fn,context){
    return function(){
        fn.apply(context,arguments);
    };
}
```

ECMAScript5为所有函数定义了一个原生的bind()方法，进一步简单了操作。

```javascript
fn.bind(this);
```

## 参考

1. 《JavaScript高级程序设计》