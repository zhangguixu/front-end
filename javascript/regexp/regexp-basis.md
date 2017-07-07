# RegExp

ECMAScript通过RegExp类型来支持正则表达式

## 1. 创建

创建一个正则表达式，有两种方式

1. 字面量【常用】

    ```javascript
    var pattern = /test/;
    ```

2. 构造RegExp实例

    ```javascript
    var pattern = new RegExp("test");
    ```

两种方式的比较：

1. 如果正则是已知的，则优先选择字面量语法
2. 如果需要在运行时通过动态构建的字符串来创建正则表达式，则通过构造器的方式

使用正则表达式字面量和使用RegExp构造函数创建的正则表达式不一样,在ECMAScript3中，正则表达式字面量始终会共享一个RegExp实例。在ECMAScrpt5明确规定,使用正则表达式字面量必须像直接调用RegExp构造函数一样，每次都创建新的RegExp实例

## 2. flags

* g:（global）表示全局模式，即模式将应用于所有字符串
* i：（case-insensitive）表示不区分大小写模式
* m：（multiline），表示多行模式，即在到达一行文本末尾时继续查找下一行

```javascript
/*
* 匹配字符串中所有"at"的实例
*/
var pattern1 = /at/g;
/*
* 匹配第一个"bat"或"cat"，不区分大小写
*/
var pattern2 = /[bc]at/i;
/*
* 匹配所有以"at"结尾的3 个字符的组合，不区分大小写
*/
var pattern3 = /.at/gi;
```

## 3. RegExp实例属性

1. global：布尔值，是否设置g标志
2. ignoreCase：布尔值，是否设置i标志
3. multiline：布尔值，是否设置m标志
4. lastIndex：整数，表示开始搜索下一个匹配的字符位置，从0算起
5. source：正则表达式的字符串表示

```javascript
	var pattern = /\[bc\]at/i;
	pattern.global;       //false
	pattern.ignoreCase;   //true
	pattern.multtline;    //false
	pattern.lastIndex;	  //0
	pattern.source;       //'\[bc\]at'
```

## 4. RegExp实例方法

### 4.1 exec()

专门为捕获组而设计的，只接收要应用模式的字符串，然后返回包含第一个匹配项信
息的数组，返回的数组是Array的实例，同时包含两个额外的属性：
    1. index（匹配项在字符串中的位置）
    2. input（应用正则表达式的字符串）
第一项是与整个模式匹配的字符串，其他项是与模式中的捕获组匹配的字符串；
如果没有匹配项则返回null。

```javascript
var text = 'mom and dad and baby';
var pattern = /mom( and dad( and baby)?)?/gi;
var matches = pattern.exec(text);
matches.index; //0
matches.input; //'mom and dad and baby'
matches[0];//'mom and dad and baby'
matches[1];//' and dad and baby'
matches[2];//' and baby'
```

## 4.2 test()

接收一个字符串参数，在该模式有参数匹配的情况下返回true，否则返回false

```javascript
var text = '000-00-0000';
var pattern = /\d{3}-\d{2}-\d{4}/;
if(pattern.test(text)){
    console.log('matched');
}
```

## 5. RegExp构造函数属性

RegExp构造函数包含一些属性，这些属性适用于作用域中的所有正则表达式，并且基于所执行的最近一次正则表达式操作而变化。

![RegExp属性](../../image/RegExp-properties.PNG)

```javascript
	var text = 'this has been a short summer';
	var pattern = /(.)hort/g;

	/*
	 *注意：Opera不支持input、lastMatch、lastParen和Multiline属性
	 *IE不支持multiline属性
	 */
	 if(pattern.test(text)){
	 	console.log(RegExp.input); //this has been a short summer
	 	console.log(RegExp.lastParen);//s
	 	console.log(RegExp['$1']);//s
	 	console.log(RegExp.lastMatch);//short
	 	console.log(RegExp['$&']); //short
	 }
```

#### 模式的局限性

不支持的特性

1. 匹配字符串开始和结尾的\A 和\Z 锚
2. 向后查找（lookbehind）
3. 并集和交集类
4. 原子组（atomic grouping）
5. Unicode 支持（单个字符除外，如\uFFFF）
6. 命名的捕获组
7. s（single，单行）和x（free-spacing，无间隔）匹配模式
8. 条件匹配
9. 正则表达式注释

## 来源

1. 《JavaScirpt高级程序设计》
2. 《JavaScript忍者秘籍》