# JSONP

## 1. 概述

jsonp是一种跨域通信的手段，它的原理其实很简单：

1. 首先是利用script标签的src属性来实现跨域。
2. 通过将前端方法作为参数传递到服务器端，然后由服务器端注入参数之后再返回，实现服务器端向客户端通信。
3. 由于使用script标签的src属性，因此只支持get方法

## 2. 实现流程

1. 设定一个script标签

    ```html
    <script src="http://jsonp.js?callback=xxx"></script>
    ```

2. callback定义了一个函数名，而远程服务端通过调用指定的函数并传入参数来实现传递参数，将`fn(response)`传递回客户端

    ```php
    $callback = !empty($_GET['callback']) ? $_GET['callback'] : 'callback';
    echo $callback.'(.json_encode($data).)';
    ```

3. 客户端接收到返回的js脚本，开始解析和执行`fn(response)`

## 3. jsonp简单实现

一个简单的jsonp实现，其实就是拼接url，然后将动态添加一个script元素到头部。

```javascript
function jsonp(req){
    var script = document.createElement('script');
    var url = req.url + '?callback=' + req.callback.name;
    script.src = url;
    document.getElementsByTagName('head')[0].appendChild(script); 
}
```

前端js示例

```javascript
function hello(res){
    alert('hello ' + res.data);
}
jsonp({
    url : '',
    callback : hello 
});
```

服务器端代码

```javascript
var http = require('http');
var urllib = require('url');

var port = 8080;
var data = {'data':'world'};

http.createServer(function(req,res){
    var params = urllib.parse(req.url,true);
    if(params.query.callback){
        console.log(params.query.callback);
        //jsonp
        var str = params.query.callback + '(' + JSON.stringify(data) + ')';
        res.end(str);
    } else {
        res.end();
    }
    
}).listen(port,function(){
    console.log('jsonp server is on');
});
```

然而，这个实现虽然简单，但有一些不足的地方：

1. 我们传递的回调必须是一个全局方法，我们都知道要尽量减少全局的方法。
2. 需要加入一些参数校验，确保接口可以正常执行。

## 4. 可靠的jsonp实现

```javascript
(function (global) {
    var id = 0,
        container = document.getElementsByTagName("head")[0];

    function jsonp(options) {
        if(!options || !options.url) return;

        var scriptNode = document.createElement("script"),
            data = options.data || {},
            url = options.url,
            callback = options.callback,
            fnName = "jsonp" + id++;

        // 添加回调函数
        data["callback"] = fnName;

        // 拼接url
        var params = [];
        for (var key in data) {
            params.push(encodeURIComponent(key) + "=" + encodeURIComponent(data[key]));
        }
        url = url.indexOf("?") > 0 ? (url + "&") : (url + "?");
        url += params.join("&");
        scriptNode.src = url;

        // 传递的是一个匿名的回调函数，要执行的话，暴露为一个全局方法
        global[fnName] = function (ret) {
            callback && callback(ret);
            container.removeChild(scriptNode);
            delete global[fnName];
        }

        // 出错处理
        scriptNode.onerror = function () {
            callback && callback({error:"error"});
            container.removeChild(scriptNode);
            global[fnName] && delete global[fnName];
        }

        scriptNode.type = "text/javascript";
        container.appendChild(scriptNode)
    }

    global.jsonp = jsonp;

})(this);
```

使用示例

```javascript
jsonp({
    url : "www.example.com",
    data : {id : 1},
    callback : function (ret) {
        console.log(ret);
    }
});
```




