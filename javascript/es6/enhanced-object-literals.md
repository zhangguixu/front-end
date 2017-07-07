# Enhanced Object Literals(增强的对象文本)

一个典型的ES5对象文本

```javascript
var serviceBase = {port:3000,url:'qq.com'},
    getAccounts = function(){return[1,2,3]};

var accountService = Object.create(serviceBase);
accountServiceES5 = {
    getAccounts : getAccounts,
    toString : function(){
        return JSON.stringify(this.valueOf());
    },
    getUrl : function() {
        return 'http://' + this.url + ':' + this.port
    },
    valueOf_1_2_3 : getAccounts()
}
```

在ES6，可以使用以下便利的写法

```javascript
var accountServiceES6 = {
    __proto__ : serviceBase, //便利的写法
    getAccounts, //便利的写法
    toString : function(){
        return JSON.stringify(this.valueOf());
    },
    getUrl : function() {
        return 'http://' + this.url + ':' + this.port
    },
    valueOf_1_2_3 : getAccounts()
}
```