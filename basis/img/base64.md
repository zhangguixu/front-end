# base64

## 1. 基本认识

Base64是网络上最常见的用于传输8bit字节代码的编码方式之一，可用于在HTTP环境下传递较长的标识信息。

示例：base64:URL就是base64编码的URL地址

```html
<img src="data:image/gif;base64,R0lGODlhAwADAIABAL6+vv///yH5BAEAAAEALAAAAAADAAMAAAIDjA9WADs=" />
```

## 2. 文件数据编码

通过HTTP传输的文件还可以通过base64对数据进行编码传输。

```css
background-image:url(data:image/gif;base64,R0lGODlhBAABAIABAMLBwfLx8SH5BAEAAAEALAAAAAAEAAEAAAICRF4AOw==);
```

## 3. 获取图片的base64编码

通过HTML5的`FileReader`可以获取一个文件的base64编码数据

```javascript
var reader = new FileReader(),htmlImage;
reader.onload = function(e){
    //e.target.result 就是base64编码
    htmlImage = '<img src="' + e.target.result + '"/>';
}
reader.readAsDataURL(file);
```

## 4. base64:URL传输图片

本质是将图片的二进制大小以一些字母的形式展开，例如一个1024字节的图片，base64编码后至少1024个字符，这个大小会被完全嵌入到css文件中。因此可以看到这种技术的一个优点

* 减少HTTP请求
* 没有图片更新要重新上传，清理缓存的问题

缺点：

* 增加了CSS文件的尺寸
* 编码成本

使用场景：解决图片大小极小，但又占据一次HTTP请求，同时不能与其他背景图片Sprite的情况