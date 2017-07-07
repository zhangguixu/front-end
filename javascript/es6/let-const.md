## Block-Scoped Constructs Let and Const

在ES5中，块级作用域是起不了作用的：

```javascript
function calculateTotalAmount(vip){
    var amount = 0;
    if(vip){
        var amount = 1;
    }
    {
        var amount = 100;
        {
            var amount = 1000;
        }
    }
    return amount;
}
console.log(calculateTotalAmount(true)); //1000
```

在ES6中，`let`限制块级作用域，`var`限制函数作用域

```javascript
function calculateTotalAmount(vip){
    var amount = 0;
    if(vip){
        let amount = 1;
    }
    {
        let amount = 100;
        {
            let amount = 1000;
        }
    }
    return amount;
}
console.log(calculateTotalAmount(true)); //0
```

在ES6中，`const`设置一个不变量

```javascript
function calculateTotalAmount(vip){
    const amount = 0;
    if(vip){
        const amount = 1;
    }
    {
        const amount = 100;
        {
            const amount = 1000;
        }
    }
    return amount;
}
console.log(calculateTotalAmount(true)); //0
```