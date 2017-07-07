# 图片上传

## 1. 传统的form表单上传

```html
<form action="" method="POST" enctype="multipart/form-data">
    <input type="file" name="img">
</form>
```

1. 必须使用`post`方法
2. `enctype`的值必须为`multipart/form-data`

## 2. 隐藏iframe模拟Ajax上传

```html
<iframe  name="ajaxUpload" style="display:none"></iframe>
<form action="url" method="post" enctype="multipart/form-data" target="ajaxUpload">
    <input type="file" name="img">
</form>
```

1. 隐藏iframe
2. form表单的target为隐藏的iframe，这样服务器返回的消息就会显示在iframe上

## 3. HTML5特性实现Ajax上传

1. 使用已有的form表单，来实例化`FormData`对象

    ```javascript
    var form = document.getElementById('form');
    var formData = new FormData(form);
    ```

2. 此外，可以使用`append`来添加更多的信息

    ```javascript
    var img = document.getElementById('img').files[0];
    var formData = new FormData();
    formData.append('img',img);
    ```

3. 利用`XHR`上传图片

    ```javascript
    var xhr = new XMLHttpRequest();
    xhr.open('POST',url);
    xhr.onreadystatechange = function(){};
    xhr.send(formData);
    ```

4. 使用`XHR2`可以获取上传的进度

    ```javascript
    xhr.upload.ongress = function(evt){
        console.log(evt);
        var loaded = evt.loaded; //已经上传的大小
        var total = evt.total;//附件总大小
        var per = Math.floor(100 * loaded / total );//已经上传的百分比
    }
    ```

## 4. 检验图片信息

HTML5提供了一些属性来检索图片信息后缀，即`file`对象的信息

* name 名字
* size 大小
* type 类型
* lastModified 最后上传的时间毫秒

例子

```html
<input type="file" id="img" name="img">
```

获取file对象

```javascript
var img = document.getElementById('img');
var files = img.files;
var file = files[0];
```