# Singleton(单例)模式

## 1. 简介

Singleton(单例)模式的理解要从两个方面

1. 它限制了类的实例化次数只能为一次，即在该实例不存在的情况下，可以通过一个方法创建对象实例，如果实例已经存在，则返回该对象的引用。
2. 它不同于静态类（对象），单例可以推迟初始化，这一点很重要，因为可以推迟初始化，就意味着不用过早地为单例分配内存资源，而是可以当需要使用时，才对其进行初始化，分配对应的内存空间。

*单例的存在，在一些时候并不是好事，可能它的出现时因为系统中的模块要么是系统紧密耦合，要么是其逻辑过分分散。*

## 2. 代码示例

```javascript
var singleton = (function () {
    var instance;
    return {
        getInstance : function (factory){
            var args = Array.prototype.slice.call(arguments, 1);
            if(!instance){
                instance = {};
                factory.apply(instance, args);
            }
            return instance;
        }
    }
})();
```

代码中创建了一个单例的包装器，使用的示例代码如下

```javascript
function Person(name, age){
    this.name = name;
    this.age = age;
    this.sayName = function () {
        console.log(this.name);
    }
}
var p1 = singleton.getInstance(Person, "zhang", 25);
var p2 = singleton.getInstance(Person);
console.log(p1 == p2); // true
console.log(p2.sayName()); // "zhang"
```

因为是单例，因此，我们可以把对象的方法直接放在this上，而不用放在Person的prototype对象上。但是这段代码还有不足，那就是只能产生一个构造器的单例，我们可以来对其进行改进。

```javascript
var singleton = (function () {
    var instances = [],
        guid = 0;
    return {
        getInstance : function (factory){
            var args = Array.prototype.slice.call(arguments, 1);
            if(typeof factory._guid == "undefined"){ 
                instances[guid] = {};
                factory.apply(instances[guid], args);
                factory._guid = guid++;
            }
            return instances[factory._guid];
        }
    }
})();
```

我们可以使用一个单例池（instances）来保存不同构造器的单例，用`_guid`来标识是否为该构造器生成单例，并作为下标索引获取该单例。使用示例：

```javascript
function Person(name, age){
    this.name = name;
    this.age = age;
    this.sayName = function () {
        console.log(this.name);
    }
}

var p1 = singleton.getInstance(Person, "zhang", 25);
var p2 = singleton.getInstance(Person);
console.log(p1 == p2); // true
console.log(p2.sayName()); // "zhang"

function Car(brand){
    this.brand = brand;
    this.sayBrand = function () {
        console.log(this.brand);
    }
}

var c1 = singleton.getInstance(Car, "benchi");
var c2 = singleton.getInstance(Car);
console.log(c1 == c2); // true
console.log(c2.sayBrand()); // "benchi"

var p3 = singleton.getInstance(Person);
console.log(p3.sayName()); // "zhang"
```
