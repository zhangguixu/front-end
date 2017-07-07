# 跨站脚本

## 定义

跨站脚本（Cross-Site scripting）,又叫XSS，这个术语表示了一类安全问题，也就是攻击者向目标Web站点注入了HTML标签或脚本。

## 攻击原理

如果一个Web页面动态地产生文档内容，并且这些文档内容是基于用户提交的数据的，而并没有通过从中移除任何嵌入的HTML标签来"消毒"的话，那么这个Web页面很容易遭到跨站脚本的攻击。

例如：脚本使用JavaScript通过用户名字来向用户问好

```javascript
var name = decodeURIComponent(window.location.search.substring(1) || '');
document.write('hello '+name);
```

考虑如果URL为

    http://www.example.com/greet.html?%3Cscript%3Ealert('David')%3C/script%3E

注入的脚本就会显示一个对话框。此外，可以把其他站点的脚本注入到目标站点中，注入的脚本就可以对站点A的内容进行任何想要的操作。

## 防止XSS攻击的办法

### 标签符号转义

在使用任何不可信的数据来动态的创建文档内容之前，对输出的内容进行标签符号的转义

```javascript
function htmlEsc(s){
    if(!s) return "";
    s = s + "";
    if(s.length == 0) return "";
    s = s.replace(/&/g, "&amp;");
    s = s.replace(/</g, "&lt;");
    s = s.replace(/>/g, "&gt;");
    s = s.replace(/ /g, "&nbsp;");
    s = s.replace(/\'/g, "&#39;");
    s = s.replace(/\"/g, "&quot;");
    s = s.replace(/\n/g, "<br>");
    return s;
}
```

*要养成这种意识*

### iframe的sandbox属性

HTML5为<iframe\>元素定义了一个`sandbox`属性，在实现之后，它运行显示不可信的内容，并自动禁止脚本。