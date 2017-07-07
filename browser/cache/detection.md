# 离线检测

## 概述

要知道设备是在线还是离线，HTML5为此定义了一个属性`navigator.onLine`，这个属性的值

* true：表示设备能上网
* false：表示设备离线

但是，这个属性的值在不同浏览器之间有着细微的差异，导致通过这个属性其实并不能准确地知道网络是否连通。

* IE6+和Safari 5+能够正确检测到网络已断开，并将这个属性设置为false
* Firefox 3+和Opera 10.6+支持navigator.onLine属性，但必须手动选中菜单项中的文件->web开发人员（设置）-> 脱机工作，才会将这个属性设置为false，否则其他情况都为true
* 在Chrome（目前还会）始终将navigator.onLine设置为true


此外，HTML5还定义了两个事件：online和offline，当网络从离线变为在线或从在线变为离线时，分别触发这两个事件。

```javascript
window.addEventListener("online", function(){}, false);
window.addEventListener("offline", function(){}, false);
```

## 来源

1. 《JavaScript高级程序设计》