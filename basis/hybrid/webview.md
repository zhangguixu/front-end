# WebView

## 概述

WebView是安卓View的一个子类，用于展示web页面的。它使用Webkit引擎对页面进行渲染，在默认情况下，WebView并没有提供像一个完整浏览器那么多的功能特性，就只是单单展示静态页面而已。

什么场景下使用WebView？

1. 应用中的内容时常会进行变更
2. 应用经常需要建立网络连接去接收数据，例如邮箱。？

这两种场景下使用WebView开发会更加方便快捷。 

## 基本使用

要在应用中添加WebView，只需要在布局配置文件中加入<WebView>元素即可，例如

```xml
<?xml version="1.0" encoding="utf-8"?>
<WebView  xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/webview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
/>
```

然后使用WebView的loadUrl()方法来加载一个网页

```java
WebView myWebView = (WebView) findViewById(R.id.webview)
myWebView.loadUrl("http://www.example.com");
```

注意给你的应用申请连接网路的权限，在manifest中配置

```xml
<manifest ... >
    <uses-permission android:name="android.permission.INTERNET" />
    ...
</manifest>
```

这样，我们就可以使用WebView来展示一个静态的页面了。

## 辅助类

WebView主要任务的是解析和渲染，为了完成其他的任务，例如资源请求、UI变更等，还需要两个辅助类WebViewClient和WebChromeClient来完成。

1. WebViewClient：接收到各种通知和请求，并且进行处理

```java
// 设置WebViewClient
webview.setWebViewClient(new WebViewClient(){

    // 对当前WebView即将要加载的URL进行处理，如果返回为true说明应用对url进行处理
    // 返回false则表示让WebView采取默认行为处理
    // 当使用POST方式时，这个方法不会被调用
    @Override
    public boolean shouldOverrideUrlLoading(WebView view, 
                                        String url){
        return true;
    }

    // 重写此方法能够处理在webview中的按键事件
    @Override
    public boolean shouldOverrideKeyEvent(WebView view, 
                                        KeyEvent event){
        return true;
    }

    // 当有资源请求时调用这个方法，如果这个方法返回null则WebView继续请求加载资源
    // 如果有返回，则返回的数据将响应WebView的资源请求。
    // 这个方法会在一个新的线程（非UI线程）中调用
    @Override
    public WebResourceResponse shouldInterceptRequest (WebView view,
                                                    String url){
        return null;
    }

    // 这个函数在开始载入页面时调用的
    @Override
    public void onPageStarted (WebView view, 
                            String url,
                            Bitmap favicon){
    }

    // 这个函数在页面加载完成之后调用
    @Ovrride
    public void onPageFinished (WebView view, 
                String url){
    }

    // 在页面中的任何资源进行http请求的时候发生的错误都会被接收到
    // http请求状态码>=400
    @Override
    public void onReceivedHttpError (WebView view, 
                                WebResourceRequest request,
                                WebResourceResponse errorResponse){
    }
});
```

2. WebChromeClient：

## 设置

辅助类是如何

## 来源

1. [WebView](https://developer.android.com/reference/android/webkit/WebView.html)
2. [Building Web Apps in WebView](https://developer.android.com/guide/webapps/webview.html)