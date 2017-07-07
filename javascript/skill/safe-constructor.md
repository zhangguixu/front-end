# 作用域安全的构造函数

这个技巧主要是避免在全局作用域内调用函数构造函数，由于没有使用new，导致在全局作用域添加冗余的属性，同时还没有得到正确的执行结果。

```javascript
//Global
function Person(name,age,job){
    this.name = name;
    this.age = age;
    this.job = job;
}
//没有使用new
var person = Person('zhang',26,'font-end');

console.log(window.name);//'zhang'
console.log(window.age);//26
```

*上述代码在严格模式下，运行会出错*

因此，需要在函数里面`确认this对象是正确类型的实例`：

```javascript
function Person(name){
    if(this instanceof Person){
    this.name = 'zhang';
    } else {
        return new Person(name)
    }
}
```

## 参考

1. 《JavaScript高级程序设计》