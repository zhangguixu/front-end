# Template Literals(模版)

如果需要拼接一个字符串，一般的做法

```javascript
var name = 'Your name is ' + first + ' ' + last + '.';
var url = 'qq.com/' + id; 
```

在ES6中，可以使用语法`${NAME}`，并把它放在反引号里

```javascript
var name = `Your name is ${first} ${last} .`;
var url = `qq.com/${id}`;
```
