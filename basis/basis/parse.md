# 页面解析

## 1. 概述

以`webkit`为例，我们来看看html文件是如何一步步被解析渲染成网页的。

![html-parse-webkitflow.png](../../image/html-parse-webkitflow.png)

可以看到一个大致的过程是

1. HTML代码转化为DOM
2. CSS代码转化成CSSOM（CSS Object Model）
3. 结合DOM和CSSOM，生成一棵渲染树（包含每个节点的视觉信息）
4. 生成布局（layout），所有渲染树的所有节点进行平面合成
5. 将布局绘制（paint）在屏幕上。

在这五步里面，第一步到第三部都非常快，`第四步和第五步很耗时`。

其他的渲染引擎的处理过程也是类似的，不过可能术语不同。例如Webkit使用的术语`布局(layout)`跟Gecko的`重排(reflow)`是同一个意思。




## 2. 学习资料


1. [浏览器的渲染原理简介](http://coolshell.cn/articles/9666.html)
2. [神文:how browser work](http://taligarsiel.com/Projects/howbrowserswork1.htm)
3. [浏览器的工作原理：新式网络浏览器幕后揭秘](http://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#The_rendering_engines_threads)
4. [WebCore Rendering](https://webkit.org/blog/114/webcore-rendering-i-the-basics/)
5. [Webkit github](https://github.com/WebKit/webkit)