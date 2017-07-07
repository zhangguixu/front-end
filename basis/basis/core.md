# 内核

## 1. 概述

浏览器最重要的就是内核，它决定了网页渲染速度和呈现效果。

浏览器内核又可以分为两部分

1. 图形引擎（layout engine/rendering engine），它负责解析HTML和CSS，渲染网页。
2. js引擎，负责解析和执行javascript脚本。

## 2. 图形引擎

在叫法上，我们经常把图形引擎叫做`内核`、或者渲染引擎。

### 2.1 Trident

Trident(MSHTML)，是IE使用的呈现引擎。在IE4中被首次使用，之后不断加入新的技术，并随着新版本的IE发布。

Trident实际上是一款开源的呈现引擎，其内部接口设计相当成熟，因此才会涌现许多采用IE内核的浏览器，例如搜狗浏览器、猎豹安全浏览器等。其中部分浏览器的新版本是所谓的双核，其中一个内核就是Trident，在加上一个其他的内核。而所谓的兼容浏览模式就是使用Trident，高速模式就是采用其他的内核。不过Trident只能用于Windows平台。

由于IE曾经的霸主地位，导致Trident内核在很长时间内都是一家独大，微软也在相当长的一段时间内没有更新Trident，一方面导致了Trident内核曾经几乎与W3C标准脱节，另一方面是Trident内核中大量的bug、安全性问题没有得到及时的解决。就在这样的情况下，很多用户转向了其他浏览器，FireFox和Opera就是这个时候兴起的。非Trident内核浏览器的市场占有率大幅提高也致使很多网页开发人员开始注意网页标准和非IE浏览器的浏览器效果问题。

### 2.2 Gecko

Gecko(Firefox内核)，Netscape6开始采用的内核，后来的Mozilla FireFox(火狐浏览器) 也采用了该内核，Gecko的特点是代码完全公开，因此，其可开发程度很高，全世界的程序员都可以为其编写代码，增加功能。因为这是个开源内核，因此受到许多人的青睐，Gecko内核的浏览器也很多，这也是Gecko内核虽然年轻但市场占有率能够迅速提高的重要原因。

事实上，Gecko引擎的由来跟IE不无关系，前面说过IE没有使用W3C的标准，这导致了微软内部一些开发人员的不满；他们与当时已经停止更新了的 Netscape的一些员工一起创办了Mozilla，以当时的Mosaic内核为基础重新编写内核，于是开发出了Gecko。不过事实上，Gecko内核的浏览器仍然还是Firefox(火狐)用户最多，所以有时也会被称为Firefox内核。此外Gecko也是一个跨平台内核，可以在Windows、BSD、Linux和Mac OS X中使用。

### 2.3 Presto

Presto是Opera前内核，Opera12.17及更早版本曾经采用的内核，现已停止开发并废弃，该内核在2003年的Opera7中首次被使用，该款引擎的特点就是渲染速度的优化达到了极致，然而代价是牺牲了网页的兼容性。
实际上这是一个动态内核，与前面几个内核的最大的区别就在脚本处理上，Presto有着天生的优势，页面的全部或者部分都能够在回应脚本事件时等情况下被重新解析。此外该内核在执行Javascrīpt的时候有着最快的速度，根据在同等条件下的测试，Presto内核执行同等Javascrīpt所需的时间仅有Trident和Gecko内核的约1/3（Trident内核最慢，不过两者相差没有多大），本文的其中一个修改者认为上述测试信息过于老旧且不完整，因为他曾做过的小测试显示Presto部分快部分慢，各内核总体相当。那次测试的时候因为Apple机的硬件条件和普通PC机不同所以没有测试WebCore内核。只可惜Presto是商业引擎，使用Presto的除开Opera以外，只剩下NDSBrowser、Wii Internet Channle、Nokia 770网络浏览器等，这很大程度上限制了Presto的发展。

Opera现已改用Google Chrome的Blink内核。

### 2.4 KHTML/WebKit

KHTML，是HTML页面渲染引擎之一，由KDE所开发。KHTML拥有速度快捷的优点，但对错误语法的容忍度则比Mozilla产品所使用的Gecko引擎小。苹果电脑于2002年采纳了KHTML作为开发Safari浏览器之用。WebCore及WebKit引擎均是KHTML的衍生产品。这一点其实在进行浏览器探嗅技术中会得到体现，可以在userAgent中KHTML的存在。

Webkit(Safari内核,Chrome内核原型,开源):它是苹果公司自己的内核，也是苹果的Safari浏览器使用的内核。 Webkit引擎包含WebCore排版引擎及JavaScriptCore解析引擎，均是从KDE的KHTML及KJS引擎衍生而来，它们都是自由软件，在GPL条约下授权，同时支持BSD系统的开发。所以Webkit也是自由软件，同时开放源代码。在安全方面不受IE、Firefox的制约，所以Safari浏览器在国内还是很安全的。

限于Mac OS X的使用不广泛和Safari浏览器曾经只是Mac OS X的专属浏览器，这个内核本身应该说市场范围并不大；但似乎根据最新的浏览器调查表明，该浏览器的市场甚至已经超过了Opera的Presto了——当然这一方面得益于苹果转到x86架构之后的人气暴涨，另外也是因为Safari 3终于推出了Windows版的缘故吧。Mac下还有OmniWeb、Shiira等人气很高的浏览器。

Google Chrome、360极速浏览器以及搜狗高速浏览器高速模式也使用Webkit作为内核(在脚本理解方面，Chrome使用自己研发的V8引擎)。WebKit 内核在手机上的应用也十分广泛，例如 Google 的手机 Gphone、 Apple 的iPhone， Nokia’s Series 60 browser 等所使用的 Browser 内核引擎，都是基于 WebKit。

WebKit内核常见的浏览器：傲游浏览器3、 Apple Safari(Win/Mac/iPhone/iPad)、Symbian手机浏览器、Android 默认浏览器，

### 2.5 Blink

Blink是一个由Google和Opera Software开发的浏览器排版引擎，Google计划将这个渲染引擎作为Chromium计划的一部分，并且在2013年4月的时候公布了这一消息。这一渲染引擎是开源引擎WebKit中WebCore组件的一个分支，并且在Chrome（28及往后版本）、Opera（15及往后版本）和Yandex浏览器中使用。

## 3. js引擎

Js引擎通常被称作一种虚拟机，准确来说，是一种进程虚拟机，专门设计来解释和执行JavaScript代码。

> 虚拟机是指软件驱动的给定的计算机系统的模拟器，有很多类型的虚拟机，它们根据在多大程度上模拟或代替真实的物理机器来分类。其中，进程虚拟机提供能运行一个程序或者进程的功能。例如Wine就是一种进程虚拟机，它允许你在Linux机器上运行Windows应用。

常见的js引擎如下：

* V8，开放源代码，由Google丹麦开发，是Google Chrome的一部分。
* Chakra (JScript引擎)，用于Internet Explorer。
* JavaScriptCore，开放源代码，用于Safari。
* SpiderMonkey，第一款JavaScript引擎，早期用于Netscape Navigator，现时用于Mozilla Firefox。
* Rhino，由Mozilla基金会管理，开放源代码，完全以Java编写。
* KJS，KDE的ECMAScript／JavaScript引擎，最初由哈里·波顿开发，用于KDE项目的Konqueror网页浏览器中。

## 4. 来源

1. [浏览器内核介绍](http://blog.csdn.net/wei_ge163/article/details/7631112)
2. [js引擎指南](http://web.jobbole.com/84351/)
3. [wiki百科](https://zh.wikipedia.org/wiki/JavaScript%E5%BC%95%E6%93%8E)