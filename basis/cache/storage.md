# storage

HTML5提供了两个新的客户端缓存方式

## LocalStorage

* 是一种本地存储的公共资源，域名下很多应用共享这份资源会有风险

* 是以页面域名划分的，如果有多个等价域名之间的LocalStorage不互通，则造成缓存多份浪费

* 在PC兼容性不好，而且当网络速度快，协商缓存响应快时，使用LocalStorage比不上304。并且不能缓存css文件。

* 在移动端，由于网速慢，使用LocalStorage要快于304

![localstorage1](../../image/localstorage1.png)

![localstorage2](../../image/localstorage2.png)

## SessionStorage

只存储到特定的会话中，不属于持久化的存储，所以关闭浏览器就会清除数据。
