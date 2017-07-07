#  Class

直接看如何用ES6写一个类。ES6没有用函数，而是使用原型实现类。

1. 方法名不需要加function关键字，也不需要冒号`:`
2. 不需要分配属性`this`

```javascript
class baseModel {
    constructor(options,data) {
        this.name = 'Base';
        this.url = 'qq.com';
        this.data = data;
        this.options = options;
    }
    getName(){
        console.log(`Class name:${this.name}`);
    }
}

class AccountModel extends baseModel {
    constructor(options,data){
        super({private:true},['1','2']);
        this.name = 'Account Model';
        this.url += '/accounts/';
    }
    get accountsData() {
        return this.data;
    }
}

let accounts = new AccountModel();
accounts.getName();
console.log(`Data is ${accounts.accountsData}`);
//Class name:Account Model
//Data is 1,2
```