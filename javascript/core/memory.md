# 内存管理

> 本文以V8为背景

## 1. 基础概念

### 1.1 生命周期

不管什么程序语言，内存生命周期基本是一致的：   

1. 分配你所需要的内存
2. 使用分配到的内存（读、写）
3. 不需要时将其释放\归还

在所有语言中第一和第二部分都很清晰。最后一步在低级语言(例如C语言)中很清晰，但是在像JavaScript等高级语言中，这一步依赖于垃圾回收机制，一般情况下不用程序员操心。

### 1.2 堆与栈

我们知道，内存空间可以分为栈空间和堆空间，其中

1. 栈空间：由操作系统自动分配释放，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈。
2. 堆空间：一般由程序员分配释放，这部分空间就要考虑垃圾回收的问题。

### 1.3 基本类型与引用类型

在JavaScript中

1. 基本类型：undefined,null,boolean,number,string，在内存中占有固定的大小，他们的值保存在栈空间中，我们通过按值来访问。

2. 引用类型：Object,Array,Function，则在堆内存中为这个值分配空间，然后把它的内存地址保存在栈内存中。（区分变量和对象）

![../image/memory-datatype.png]

### 1.4 V8的变量存放

![v8-stack-heap.jpg](../image/v8-stack-heap.jpg)

* handle

    handle是指向对象的指针，在V8中，所有对象都是通过handle来引用，handle主要用于V8的垃圾回收机制。进一步的，handle分为两种：

    * 持久化(Persistent handle)，存放在堆上
    * 本地化(Local handle)，存放在栈上

* scope

    scope是handle的集合，可以包含若干个handle，这样就无需将每个handle逐次释放，而是直接释放整个scope。

* context

    context是一个执行器环境，使用context可以将相互分离的JavaScript脚本在同一个V8实例中运行，而不互相干涉。在运行JavaScript脚本时，需要显示的指定context对象。

## 2. 垃圾回收

### 2.1 分代策略

脚本中，绝大多数对象的生存期很短，只有某些对象的生存期较长。为利用这一特点，V8将堆进行了分代。对象起初会被分配在新生区。在新生区的内存分配非常容易：我们只需保有一个指向内存区的指针，不断根据新对象的大小对其进行递增即可。当该指针达到了新生区的末尾，就会有一次清理（小周期），清理掉新生区中不活跃的死对象。对于活跃超过2个小周期的对象，则需将其移动至老生区。而在老生区则使用标记清除的算法来进行垃圾回收。V8通过分别对新生代对象和老生代对象使用不同的垃圾回收算法来提升来及回收的效率。这就是所谓的`分代策略`。

*默认情况下，64位环境下的V8引擎的新生代内存大小32MB、老生代内存大小为1400MB，而32位则减半，分别为16MB和700MB*

根据`分代策略`，V8将堆空间进行了分隔：

* 新生区

    大多数对象被分配在这里，新生区是一个很小的区域，垃圾回收在这个区域非常频繁，与其他区域相独立。

* 老生指针区

    这里包含大多数可能存储指向其他对象的指针的对象，大多数在新生区存活了一段时间（2个周期）的对象都会被挪到这里。

* 老生数据区

    这里存放只包含原始数据的对象，这些对象没有执行其他对象的指针，例如字符串，数字数组等，它们在新生区存活了一段时间后会被移动到这里。

* 大对象区

    每一个区域都是由一组内存页构成的。除大对象区的内存页较大之外，每个区的内存页都是1MB大小，且按1MB内存对齐。对象超过一定大小时就会被放置到这个区，垃圾回收期从不移动这个区域的对象。

* 代码区

    代码对象，也就是包含JIT之后指令的对象，会被分配到这里。这里是唯一拥有执行权限的内存区。（如果代码对象因过大而被放到大对象区，则该大对象所对应的内存也是可执行的。）

* Cell区、属性Cell区、Map区

    这些区域存放Cell、属性Cell和Map，每个区域因为都是存储相同大小的元素，因此内存结构很简单，这里也是为了方便进行回收。

*在 node-v4.x 之后，区域进行了合并为：新生区，老生区，大对象区，Map区，Code区*

此外，对于一个对象所占的内存空间，也涉及两个概念：`shallow size`和`retained size`。

* shallow size就是对象本身占用内存的大小，不包含其引用的对象。常规对象（非数组）的shallow size有其成员变量的数量和类型决定
* retained size是该对象自己的shallow size，加上从该对象能直接或间接访问到对象的shallow size之和。换句话说，retained size是该对象被GC之后所能回收到内存的总和。

这两个概念在使用chrome的开发工具中会看到。

垃圾回收释放的内存即为Retained Size的大小。

### 2.2 新生区的半空间分配策略

新生代使用半空间（Semi-space）分配策略，其中新对象最初分配在新生代的活跃半空间内。一旦半空间已满，一个Scavenge操作将活跃对象移出到其他半空间中，被认为是长期驻存的对象，并被晋升为老生代。一旦活跃对象已被移出，则在旧的半空间中剩下的任何死亡对象被丢弃。

具体的如下：

YG被平分为两部分空间From和To，所有内存从To空间被分配出去，当To满时，开始触发GC。

例如说：

某时刻，To已经为A、B和C分配了内存，当前它只剩下一小块内存未分配。而From所有的内存都空闲着。

![to-from-1.jpg](../image/to-from-1.jpg)

此时，一个程序需要为D分配内存，但D需要的内存大小超出了To未分配的内存，此时触发GC，页面停止执行

![to-from-2.jpg](../image/to-from-2.jpg)

接着From和To进行对换，即原来的To空间被标志为From，From被标志为To。并且把活的变量值（B）标志出来，而垃圾（A、C）未被标志，它们将会被清掉。

![to-from-3.jpg](../image/to-from-3.jpg)

活跃的变量（B）会被复制到To空间，而垃圾（A、C）则被回收。同时，D被分配到To空间，最后的情况如下。

![to-from-4.jpg](../image/to-from-4.jpg)

至此，整个GC完成，此过程中页面会阻塞，所以要尽可能的快。

#### 2.2.1 对象的晋升

当一个新生代的对象在满足一定条件下，会从新生代被移到老生代，这就是对象的晋升。具体的移动的标准有两种

1. 对象从From空间复制到To空间时，会检查它的内存地址来判断这个对象是否经历过一次新生代的清理结果，如果是（说明存活了两个周期了），则赋值到老生代中，否则则赋值到To空间中。

2. 对象从From空间复制到To空间时，如果To空间已经被使用了超过25%，那么这个对象直接被复制到老生代。

### 2.3 老生代

V8在老生代中采用Mark-Sweep和Mark-Compact相结合的垃圾回收策略。

#### 2.3.1 标记

标记-清除算法分为标记和清除两个阶段。

标记阶段，所有堆上的活跃对象都会被标记，每个内存页有一个用来标记对象的位图，位图中的每一位对应的内存页中的一个字，这个位图需要占据一定的空间。另外还有两位用来标记对象的状态：

* 如果一个对象为白对象，表示还未被垃圾回收器发现
* 如果一个对象为灰对象，表示已经被垃圾回收器发现，但其邻接对象尚未处理
* 如果一个对象为黑对象，表示已经被垃圾回收器发现，其邻接对象已全部处理

那么这里怎么理解标记的过程？这就必须知道：内存管理方式实际上基于`图`的概念。

GC Root是内存的根节点，在浏览器中它是window，在Nodejs中则是global对象

* 图的节点名称是创建它的构造函数名
* 图的边是引用它的属性名或者变量名

有很多内部的GC Root对用户来说都不是很重要，从应用的角度来说有下面几种情况：

* 全局变量或者全局函数会一直被window这种全局对象所指向，它们会一直占据着内存
* DOM节点只有在被javascript对象引用的情况下，会留在内存中。
* 在进行debug或者console的时候，可能会由于保留了上下文，导致本该被释放的对象被保留下来。

实际上，标记的过程正是以由GC Root建立的图为基础，来实现对象的标记，标记算法的核心是深度优先搜索，大致实现如下：

1. 初始时，位图为空，所有对象都是白对象。
2. 从根对象（GC Root）到达的对象会被染为灰色，放到一个单独的双端队列中。
3. 标记阶段，每次都会从双端队列中取出一个对象，并将其转变为黑对象，其邻接对象转变为灰，然后把其邻接对象加入到双端队列中。
4. 如果双端队列为空或者所有对象都变成黑对象，则结束。

这个算法实现起来还是蛮繁琐的，从`图`的角度来看，其实标记的过程实际上是区分活节点和垃圾节点的过程。

* 从GC Root开始遍历图，所有能到达的节点称为活节点。
* GC Root不能到达的节点，该节点就成为垃圾，将会被回收。

标记结束后，所有的对象非黑（活跃节点）即白（垃圾节点）。

标记时间取决于必须标记的活跃对象的数目，对于一个大的web应用，整个堆栈的标记可能需要超过100ms。由于全停顿会造成了浏览器一段时间无响应，所以V8使用了一种增量标记的方式标记活跃对象，将完整的标记拆分成很多小的步骤，每做完一部分就停下来，让JavaScript的应用线程执行一会，这样垃圾回收与应用线程交替执行。V8可以让每个标记步骤的持续时间低于5ms。

举个例子：

```javascript
window.ob = 2;
window.oa = {
    b1 : 3,
    b2 : {
        c1 : 4,
        c2 : "字符串"
    }
};
window.ob = undefined;
```

![gc-root-tree.png](../image/gc-root-tree.png)

例如图中灰色的节点，它原来代表ob变量值，当`window.ob = undefined`后，此节点与GC Root连接的路径ob被切断了，它就成了垃圾，将会被回收。

#### 2.3.2 清除（Sweep）

由于标记完成后，所有对象都已经被标记，即不是活跃对象就是死亡对象，堆上有多少空间已经确定。清除时，垃圾回收器会扫描连续存放的死对象，将其变成空闲空间。这个任务是由专门的清扫线程同步执行。

### 2.3.3 整理（Compact）

标记清除有一个问题就是进行一次标记清楚后，内存空间往往是不连续的，会出现很多的内存碎片。如果后续需要分配一个需要内存空间较多的对象时，如果所有的内存碎片都不够用，将会使得V8无法完成这次分配，提前触发垃圾回收。

标记整理正是为了解决标记清除所带来的内存碎片的问题。标记整理在标记清除的基础进行修改，将其的清除阶段变为紧缩极端。在整理的过程中，将活着的对象向内存区的一段移动，移动完成后直接清理掉边界外的内存。紧缩过程涉及对象的移动，所以效率并不是太好，但是能保证不会生成内存碎片。

### 2.4 垃圾回收总结

1. 新生代对象的Scavenge，这通常是快速的；

2. 通过增量方式的标记步骤，依赖于需要标记的对象数量，时间可以任意长；

3. 完整垃圾回收，这可能需要很长的时间；

4. 带内存紧缩的完整垃圾回收，这也可能需要很长的时间，需要进行内存紧缩。

## 3. 内存问题

### 3.1 内存泄漏

内存泄漏是指计算机可用内存的逐渐减少，原因通常是程序持续无法释放其使用的临时内存。

先来一个最简单的DOM泄漏的例子

```javascript
var el = document.getElementById("_p");
el.mark = "marked";

//移除P
function removeP() {
    el.parentNode.removeChild(el);
    // el = null;
}
```

程序非常简单，只是把id为_p的HTML元素从页面移除，在移除之前从GC Root遍历此P元素有两条路可走。在执行`removeP()`之后，按理来说该元素应该成为垃圾，所占有的内存应该被释放掉，但是由于还存在这路径el没有被切断，p元素占有的内存无法被释放，导致了内存泄漏。

![_p.png](../image/_p.png)

### 3.2 内存占用过多

这个问题很容易理解。例如使用事件代理来减少事件监听的函数，从而减少内存分配的开销。

### 3.3 gc卡顿 

如果你的页面垃圾回收很频繁，那说明你的页面可能内存使用分配太频繁了。频繁的GC可能也会导致页面卡顿。

在一些框架中，如果创建一个大对象之后，可能不会很快就将其释放，而是会缓存起来，直到没有用处为止。

## 4. chrome dev tools

在使用Chrome进行内存分析的时候，要先在chrome菜单-》工具，或者直接按shift+esc，找到内存管理器，然后选上JavaScript使用的内存(JavaScipt Memory)。

### 4.1 timeline

通过Timeline的内存模式，可以在宏观上观察到web应用的内存情况，一般我们需要关注的点：

1. GC的时间长度是否正常？
2. GC频率是否正常？过于频繁会导致卡顿
3. 内存趋势图是否正常？
4. DOM趋势图是否正常？

这些关注点都可以在timeline的内存视图中看到，如图

![timeline-memory](../image/timeline-memory.png)

timeline统计的内存变化主要有：

* js heap：堆空间
* documents：文档计数
* node：dom节点数
* event listener：事件监听器
* CPU：在手机端暂时没有

此外还可以通过`event log`看到这期间页面执行的操作

### 4.2 profile

`profile`面板我们关注的是`Take Heap Snapshot`和`Recode Heap Allocations`

![timeline-profile.png](../image/timeline-profile.png)

profile使用必须知道的：

1. 标志为黄色的表示可能内存泄漏
2. 标志为红色表示应该是发生内存泄漏

在profile中的几个概念：

1. (global property):全局对象，还有全局对象引用的对象
2. (closure):闭包，这里需要关注一下
3. (compiled code):V8会先代码编译成特定的语言，再执行
4. (array,string,number,regexp):这些内置对象的引用
5. HTML..Element：dom对象的引用

### 4.2.1 Take Heap Snapshot

使用快照，必须知道：

1. 每次进行快照时，chrome都会先自动执行一个gc
2. 只有活跃的值，才会反映在快照里

快照有三个视图，它们分别有各自的作用

1. [Summary View](https://developer.chrome.com/devtools/docs/heap-profiling-summary)

    默认是以概要视图显示的，显示了对象总数，可以展开显示具体内容

2. [Comparison View](https://developer.chrome.com/devtools/docs/heap-profiling-comparison)

    该视图用来对照不同的快照来找到快照之间的差异

3. [Containment View](https://developer.chrome.com/devtools/docs/heap-profiling-containment)

    在这个视图中，包括三个点

    * DOMWindow objects：js中的全局对象
    * GC Root：VM垃圾回收所使用的GC Root
    * Native Object：被放置到VM中的内置对象

    好吧。暂时不知道有什么用？以后再补充。

### 4.2.2 Recode Heap Allocations

这个功能可以动态监控，通过次工具可以看到

1. 什么时候分配了内存，刚刚分配的内存会以深蓝色的柱子表示，柱子越高，内存越大
2. 什么时候回收了内存，内存被回收的时候，柱子变为灰色

## 4.3 实践

[例子1](https://developer.chrome.com/devtools/docs/demos/memory/example1)：timeline来查看正常的内存

![timeline-memory-normal.png](../image/timeline-memory-normal.png)

[例子2](https://developer.chrome.com/devtools/docs/demos/memory/example1)：通过timeline来发现内存泄漏

![timeline-memory-1](../image/timeline-memory-1.png)

可以看到随着时间的增长，页面占用的内存越来越多，

在这种情况下就可以怀疑有内存泄漏了，也有可能是浏览器还没有进行gc，这个时候我们可以强制进行垃圾回收（垃圾筒图标）

反复测试，如果发现无论怎么样，内存一直在增长，那么估计你就遇到内存泄漏的问题了。

如果页面中DOM节点的数量一直在攀升，那么肯定出现DOM泄漏了

![timeline-memory-nodes.png](../image/timeline-memory-nodes.png)

例子3：验证快照之前会进行gc

```javascript
function Test (s) {
    this.s = s;
}
var _test1 = new Test("__________test___1_________");
var _test2 = new Test("__________test___2_________");
new Test("你看不到我，就是这么神奇");
```

![timeline-snapshot.png](../image/timeline-snapshot.png)

[例子4](https://developer.chrome.com/devtools/docs/heap-profiling-comparison)：通过snapshot来发现内存泄漏

1. 打开例子之后，先进行一次快照
2. 点击action，代表这用户的交互
3. 再进行一次快照
4. 使用comparison视图，对比两次快照，如图

![comparison](../image/timeline-snapshot-comparison.png)

可以看到，action之后，内存的数量是增加的（注意，已经gc过了），这说明web应用极有内存泄漏。

*一个原则就是找到本不应该存在却还存在的那些值。*

[例子5](https://developer.chrome.com/devtools/docs/heap-profiling-comparison)：通过内存分配的情况来分析

![timeline-allcation.png](../image/timeline-allcation.png)

点击蓝色的柱子，可以看到详细的情况，来进行分析

[例子6](https://developer.chrome.com/devtools/docs/demos/memory/example2)：通过timeline来分析gc过于频繁导致卡顿的问题

![timeline-gc](../image/timeline-gc.png)

此例子在移动手机的浏览器进行测试，页面还是相对简单，在比较复杂的移动web应用，这种情况还是比较危险的，可能会导致页面卡死。

## 参考

1. [MDN:内存管理](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Memory_Management)
2. [Chrome开发者工具之JavaScript内存分析](http://web.jobbole.com/81915/)
4. [Google V8的垃圾回收引擎](http://www.infoq.com/cn/news/2015/08/Google-V8)
5. [测试例子](https://developer.chrome.com/devtools/docs/demos/memory)
7. [如何编写避免垃圾开销的实时Javascript代码](https://gold.xitu.io/entry/575d14937db2a2005437df32)
8. [详解js变量、作用域及内存](https://segmentfault.com/a/1190000000687844)
9. [V8 concept](https://github.com/yjhjstz/deep-into-node/blob/master/chapter2/chapter2-0.md)
10. [浅谈V8引擎中的垃圾回收机制](http://blog.csdn.net/shmnh/article/details/50910179)
11. [使用 Google V8 引擎开发可定制的应用程序](https://www.ibm.com/developerworks/cn/opensource/os-cn-v8engine/)
12. [a tour of v8 garbage collection](http://www.jayconrod.com/posts/55/a-tour-of-v8-garbage-collection)
