# Module(模块)模式

## 1. 概述

模块是任何强大应用程序架构中不可或缺的一部分，它通常能够帮助我们清晰地分离和组织项目中的代码单元。

在Javascript中，有几种用于实现模块的方法，包括：

* 对象字面量表示法
* Module模式
* AMD模式
* CommonJS模块
* ECMAScript Harmony模块

## 2. 对象字面量

使用对象字面量，我们很容易就可以定义一个模块。

```javascript
var Module = {
    // 属性
    property : "value",

    // 配置选项
    config : {
        useCaching : true,
        language : "ch"
    },

    // 方法
    method1 : function (){},
    method2 : function (){},
    method3 : function (){
        // 使用配置
        console.log(this.config.useCaching + this.config.language);
    }
}

// 模块使用
Module.config.useCaching = false;
Module.method1();
Module.method2();
Module.method3();
```

## 3. Module模式

Module模式最初被定义为一种在传统软件工程中为类提供私有和公有封装的方法。在JavaScript中，Module模式用于进一步模拟`类`的概念，通过这种方式，能够使一个单独的对象拥有公有\私有方法和变量，从而屏蔽来自全局作用域的特殊部分。

*这样做可以降低函数名与在页面上的其他脚本定义的函数冲突的可能性。*

### 3.1 私有

在javascript中如何实现`私有`，换句话说就是`封装`，在代码即对变量的控制访问，外部无法直接访问到变量，必须通过提供的方法才能对变量进行操作？

答案就是`闭包`。（其实如果可以使用ECMAScript5的话，就可以使用defineProperty来对属性进行控制访问，但是闭包是一个通用性更加高的解决方案，而且也是js一个很重要的特性。）

### 3.2 示例

我们采用闭包来实现私有，闭包是什么？它是有权访问外部作用域的函数，因此，这里必须按照两步来进行：

1. 创建一个外部作用域（即函数作用域），一般来说可以使用立即执行函数来创建。
2. 创建一个内部函数来访问外部作用域的变量，提供变量的控制访问。

```javascript
// 使用立即执行函数来创建一个外部函数作用域
var Counter = (function (){
    var counter = 0;

    return {
        // 公有变量
        id : "counter01",
        // 公有方法
        incrementCounter : function () {
            return ++counter;
        },
        resetCounter : function () {
            counter = 0;
            console.log("reset!");
        }
    };
})();

// 使用示例
Counter.incrementCounter(); // 0
Counter.incrementCounter(); // 1
Counter.incrementCounter(); // 2
Counter.resetCounter(); // reset
```

这段代码返回了一个匿名对象，这个匿名对象中的方法`incrementCounter`和`resetCounter`就是两个闭包，它们实现了对变量`counter`的访问控制。

### 3.3 Module模式的优缺点

优点：

1. 相比真正封装的思想，它对于很多拥有面向对象背景的开发人员来说更加整洁。
2. 它支持私有数据，因此，在Module模式中，代码的公有部分能够接触私有部分，然而外界无法接触类的私有部分。

缺点：

1. 由于访问公有和私有成员的方式不同，当我们想改变可见性时，就必须要修改每一个曾经使用过该成员的地方。
2. 我们也无法访问那些之后在方法里添加的私有成员。

### 3.4 Revealing Module(揭示模块)模式

这个模式产生的原因是

1. 当我们想从另一个方法调用一个公有方法或访问公有变量时，必须要重复主对象的名称
2. 使用Module模式时，必须要切换到对象字面量表示法来让某种方法变成公有方法。

因此，在Module模式的基础上，Heilmann提出了一种改进的模式，能够在私有范围内简单定义所有的函数和变量，并返回一个匿名对象，它拥有指向私有函数的指针，该函数是他希望展示为公有的方法。

```javascript
var revealingModule = (function () {
    var privateVar = "1",
        publicVar = "2";
    
    function privateFn () {
        console.log("Name: " + privateVar);
    };

    function publicSetName (str) {
        privateVar = str;
    }

    function publicGetName () {
        privateFn();
    }

    // 直接暴露指针
    return {
        setName : publicSetName,
        greeting : publicVar,
        getName : publicGetName
    }
})();
```

其实咋看之下，和Module模式并没有多大的区别，最大的不同就在于返回的匿名对象是包含了公有方法和属性的`指针`。

其实这个模式的核心就在于`揭示`，也就是说在模块代码底部，它可以很容易地指出哪些函数和变量是可以被公开访问的，从而改善代码的可读性。