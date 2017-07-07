# 基本类型与包装类

## 基本类型

JavaScript有5个基本类型

* number
* string
* boolean
* undefined
* null

## 基本包装类型

为了方便操作基本类型值，ECMAScript提供了3个特殊的引用类型：

* Boolean
* Number
* String，

实际上，每当读取一个基本类型值的时候，后台就会创建一个对应的基本包装类型的对象，从而能让我们能够调用一些方法来操作这些数据。

例如：

```javascript
var s1 = 'some text';
var s2 = s1.substring(2);
```

这个代码还会以下的步骤

1. 创建一个String类型的实例
2. 在实例上调用指定的方法
3. 销毁这个实例

自动创建的基本包装类型的对象，只存在于一行代码的执行瞬间，然后立即被销毁，这意味这我们不能在运行时为基本类型值添加属性和方法

```javascript
var s1 = 'some text';
s1.color = 'red';
console.log(s1.color);//undefined;
```

*可以显式地创建基本包装类型的对象，但是不建议。*