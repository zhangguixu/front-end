# CSP

## 1. 概念

 CSP（Content Security Policy）即内容安全策略，是一种基于内容的声明式网络应用程序机制，对缓解内容注入漏洞（例如XSS）的危害非常有效。

 1. 工作原理:通过一系列指令告诉客户端（如浏览器）被保护资源（如页面）内只允许加载和执行指令集中限定的内容，类似白名单机制，不满足限定条件的资源和内容将被客户端阻断或不被执行。

2. 目标定位：有效减少内容注入漏洞发生后所造成的危害（而非防止内容注入漏洞的发生）

3. 限制范围：当前页面本身内容以及加载到当前页面的任何内容（包括页面加载完后通过js异步加载的内容）

## 2. 兼容性

`CSP Level 1`标准，目前的支持程度很高，特别是移动端，该标准定义了强大的指令集，可以非常有效降低XSS漏洞的危害。而对于不支持CSP的浏览器，只是多了一些浏览器不能识别的信息，不会有什么影响，可以放心大胆地利用起来。

[查阅兼容性的更多信息](http://caniuse.com/#feat=contentsecuritypolicy)

*配置的时候需要特别小心，可能会对现有业务功能产生影响，导致内容不可用。*

## 3. CSP指令

语法规则

`
指令1 值 值;指令2 值 值;指令3 值
`

### 3.1 指令

CSP Level 1的指令

|指令|说明|
|:--|:--|
|default-src|针对所有类型资源采取默认的加载策略，可覆盖|
|script-src|针对JavaScript的加载策略|
|style-src|针对样式的加载策略|
|img-src|针对图片的加载策略|
|connect-src|针对Ajax、webSocket等请求的加载策略，不允许时浏览器返回400|
|front-src|针对web font的加载策略|
|object-src|针对&lt;object&gt;&lt;embed&gt;&lt;applet&gt;等flash插件的加载策略|
|media-src|&lt;audio&gt;&lt;video&gt;等标签引入的html多媒体的加载策略|
|frame-src|针对frame的加载策略，不推荐使用，在CSP Level 2被舍弃|
|sandbox|对请求的资源启用sandbox，类似于iframe的sandbox|
|report-uri|记录页面请求不被策略允许资源的日志信息，并上报到该地址|

CSP Level 2的指令

|指令|说明|
|:---|:---|
|base-uri|限制当前页的url|
|child-src|限制子窗口（iframe、弹窗）的源，取代frame-src|
|form-action|限制表单能够提交到的源|
|frame-ancestors|限制了当前页面可以被哪些页面以iframe,frame,object等方式加载|
|plugin-types|限制插件的类型，例如pdf|

### 3.2 特殊取值

|指令值|说明|
|:---|:---|
|&nbsp;|指令值为空，不做任何限制|
|'none'|不允许任何内容(要加引号)|
|'self'|允许同源的内容|
|'unsafe-inline'|允许加载inline资源，例如style，onclick，inline js,css等|
|'unsafe-eval'|允许加载动态js代码，例如eval()|
|data:|允许data:协议，如base64编码的图片|
|www.example.com|允许加载指定域名的资源|
|*.example.com|运行加载example.com任何子域的资源|
|https|运行加载https资源|
|https://example.com|允许加载example.com的https资源|

### 3.3 csp默认行为

#### 3.3.1 阻止内联代码执行

CSP除了使用白名单机制外，默认配置下组织内联代码执行是防止内容注入的最大安全保障。

1. script标签

    ```html
    <script>
        alert("Hello CSP");
    </script>
    ```

2. 内联事件

    ```html
    <a href="" onclick="handleClick()"></a>
    <a href="javscript:handleClick()"></a>
    ```

3. 内联样式

    ```html
    <div style="display:none"></div>
    ```

在这种情况下，如果要开启内联的话，需要配置

```javascript
script-src 'unsafe-inline';style-src 'unsafe-inline'
```

#### 3.3.2 EVAL相关功能被禁用

用户输入字符串，然后经过eval()等函数转义进而被当作脚本去执行。这样的攻击方式比较常见。于是乎CSP默认配置下，eval() , new Function() , setTimeout([string], …) 和setInterval([string], …)都被禁止运行。

如果想要开启这些功能，可以使用`'unsafe-eval'`值

### 3.4 CSP上报日志

为了能让违规的日志信息上报，必须使用`report-uri`指令，并至少提供一个接收地址

```javascript
Content-Security-Policy: report-uri url
```

违规报告为一个json对象，包含一下的数据：

```javascript
{
    blocked-uri : 被阻止的违规资源
    document-uri : 拦截违规行为发生的页面
    original-policy : Content-Security-Policy头策略的所有内容
    referrer : 页面的referrer
    status-code : HTTP响应状态
    violated-directive : 违规的指令
}
```

## 4. 使用方式

按照使用场景的不同，大致有三种使用方式。

### 4.1 策略制定处理，测试csp策略

我们可以在页面头部添加

```html
<meta http-equiv="Content-Security-Policy" content="default-src 'self';script-src 'self' *.example.com">
```

特点是

1. 实现简单，只对当前页面有效
2. 部分指令无效，例如report-uri（即无法上报）

### 4.2 线上验证观察

需要在页面响应头添加`Content-Security-Policy-Report-Only`，这个时候只上报不符合策略的相关信息，不会真正阻止内容加载和执行。

### 4.3 线上最终部署

通过页面响应头`Content-Security-Policy`，通过服务端配置即可对多个页面有效，一旦部署，只要浏览器支持CSP标准，则不符合策略的内容都将不被加载和执行。

### 4.4 实践建议

CSP是基于内容白名单限制的方式阻止XSS攻击，一旦内容不满足条件则不会被加载和执行，因此，在制定策略的时候如果阻止了正常的内容，那可能导致业务功能受影响，这一点需要特别注意。

1. 根据自己业务访问资源情况初步确定CSP内容，
2. 确定资源白名单，可以使用策略`default-src 'none'`，先阻止出了页面本身的所有其他内容的加载和执行，然后通过chrome控制台的提示的信息，确定哪些需要放进白名单，即可得到初步的策略
3. 服务器配置`Content-Security-Policy-Report-Only`响应头，并指定上报url，用来收集阻止详情信息，以进一步验证和指导最终策略制定。
4. 根据3和分析结果确认最终策略后，在服务器配置真正起作用的CSP响应头策略，同时添加`report-uri`指令来收集阻止内容的详情数据，以进行统计分析及采取进一步行动。


