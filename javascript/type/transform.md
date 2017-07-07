# 数据类型转换

1. 在js中，隐式数据类型转换大量存在，容易发生错误
2. 显示数据类型转换

## 1. 字符串转为数字

### 1.1 显示转换

* Number()
* parseInt()
* parseFloat()

```javascript
Number('100x');   //NaN
parseInt('100x'); //100
```

Number()中的字符串必须是都是数字，而parseInt和parseFloat则会解析字符串，尽可能转为数字

### 1.2 隐式转换

```javascript
'100'-1 //99
+ '100' //100,+号作为单目运算，是正号运算
```
*注意*

```javascript
'100' + 1 // 1001
1 + '100' // 1001
```

### 1.3 特殊转换

* 无法被转换为数值的字符串 => NaN
* 空字符串 => 0

## 2. 数字转为字符串

### 2.1 显式

```javascript
String(1); // '1'
1.toString();//'1'
```

### 2.2 隐式

```javascript
foo + '100' //foo100
100 + 'foo' //100foo
```

### 2.3 特殊数值

* 数值NaN  => 'NaN'
* 数值Infinity  =>   'Infinity'
* 数值-Infinity =>   '-Infinity'

## 3. 转为boolean

### 3.1 为false的情况

* 0
* NaN
* null
* undefined
* ' '(空字符串)

### 3.2 !!进行转换

```javascript
!!'foo' // true
!!0 //false
```

## 4. 从Object类型转换为基本数据类型

### 4.1 Object转为数字

一般情况下，Object类型是无法直接转为数字的。

```javascript
var obj = {};
obj++;
obj;  //NaN
```

对象要有能返回恰当数值的valueOf方法，则可以

```javascript
var obj = {};
obj.valueOf = function() {
	return 1;
}
obj++;
obj; //2
```

### 4.2 Object转为字符串

可以通过改写Object.toString()来实现自己想要的类型转换

### 4.3 数组类型转为基本数据类型

1. [] =>

    - ''(空字符串)
    - 0
    - false

2. [9] =>

    - 9
    - '9'
    - true

## 5. 数字取整

~是一个按位取非的操作，返回数值的反码，是二进制操作，JavaScript中的number都是double类型的，在位操作的时候会转化为int，两次就还是原来的数

```javascript
~~3.1415 // 3 等同于parseInt()
~~5.678 //5
```