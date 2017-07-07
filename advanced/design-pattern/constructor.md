# Constructor(构造器)模式

在经典的面向对象编程语言中，Constructor是一种在内存已分配给该对象的情况下，用于初始化新创建对象的特殊方法。

在JavaScript中，也有所谓的构造器函数，一样是通过`new`关键字来实例化一个新的对象，并且对象的成员由该函数定义。但是JavaScript是不支持类的概念（至少目前是这样），因此它的构造器函数还是有点不太一样。

我们来对比一下java和javascript的不同。

```java
public class Person{
    // 属性
    private String name;

    // 构造器
    public Person(name){
        this.name = name;
    }

    // 方法
    public String getName(){
        return this.name;
    }
}

// 生成一个Person类的对象
Person p = new Person("zhang");
System.out.println(p.getName()); // zhang
```

在JavaScript中，要想跟以上的java代码实现差不多的效果，则

```javascript
function Person(name){
    this.name = name;
}
Person.prototype.getName = function () {
    return this.name;
}

// 生成一个Person对象实例
var person = new Person("zhang");
person.getName(); // zhang
```

我们可以看到两者明显的区别：

1. 声明方法，由于原型链的机制，要想Person对象的所有实例`共享`getName()方法，就必须在Person对象的原型上声明该方法，而不是采用以下的代码

    ```javascript
    function Person(name){
        this.name = name;
        this.getName = function () {
            return this.name;
        }
    }
    ```

2. 关于Javascript的构造器Person函数，它是可以直接执行，即

    ```javascript
    Person(); // 执行结果会是怎么样？可以思考一下，可以增加对函数理解
    ```
