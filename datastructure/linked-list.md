# 链表

## 1. 基础知识

链表是常用的数据结构之一，其基础操作有：

1. 判断是否为空
2. 统计链表节点的个数
3. 添加
4. 删除
5. 插入

链表分为单向链表和双向链表，其不同在于节点。

> 链表的操作不难实现，最主要的是各种边界条件的考虑。

## 2. 链表的反转

链表的反转分为两种情况：

1. 单向链表

    ![linkedlist-reverse](../image/linkedlist-reverse.png)
    三值交换的问题

2. 双向链表

    ![doublelinkedlist-reverse](../image/doublelinkedlist-reverse.png)

## 3. 链表操作的编码技巧

### 3.1 dummy node

Dummy node（哑节点）是链表问题中的一个重要的技巧，它是一个虚拟节点，就是在链表表头head前加一个节点指向head，即`dummy -> head`。

它的作用是保证链表的head不会在`删除`操作中丢失，当链接的head有可能变化时，使用dummy node可以很好的简化代码，最终返回dummy.next即新的链表。

### 3.3 快慢指针

快慢指针指的是向前移动的步长，每次移动的步长较大的即为`快指针`，步长较小的即为`慢指针`。常用的快慢指针一般是在单链表中让快指针每次向前移动2，慢指针则每次向前移动1。快慢指针主要有两个应用：

1. 快速找出未知长度的链表的中间节点

    设置两个指针`fast`和`low`，都指向单链表的头节点，由于`fast`的移动速度是`slow`的2倍，所以当`fast`指向末尾节点的时候，`slow`刚好就在中间。

2. 判断单链表是否有环

    利用快慢指针的原理，由于`fast`的移动速度是`slow`的2倍，如果`fast == null`说明该单链表以null结尾，不是循环链表；如果`fast == slow`，则快指针追上慢指针，说明该链表是循环链表。

## 4. 单向链表的代码实现


首先，定义一个基础节点

```javascript
class Node {
    constructor (val) {
        this.value = val;
        this.next = null;
    }
    get next () {
        return this._next;
    }
    set next (next ){
        this._next = next; 
    }
    get value () {
        return this._value;
    }
    set value (value) {
        this._value = value;
    }
}
```

在上述定义的基础上，定义链表，实现链表的操作。

```javascript
class SingleLinkedList {
    // 方便起见，使用一个数组来初始化一个链表
    constructor (arr) {
       for (let i = 0; i < arr.length; i++) {
          this.append(new Node(arr[i])); 
       } 
    }
    get head () {
        return this._head;
    }
    set head (head) {
        this._head = head;
    }

    // 定义基础操作，即成员方法

    // 判断是否为空
    isEmpty () {
        return (this.head == null);
    }
    // 返回节点的个数
    size () {
        let count = 0, cur = this.head;
        while (cur != null) {
            count += 1;
            cur = cur.next;
        }
        return count;
    }
    // 添加操作，头结点存储值·
    append (node) {
        let cur = this.head;
        if (this.isEmpty()) {
            this.head = node;
            return;
        }
        while(cur.next != null) {
            cur = cur.next;
        }
        cur.next = node;
    }
    // 删除
    del (val) {
        if (this.head.value == val) {
            this.head = this.head.next;
            return;
        }
        let pre = this.head,
            cur = pre.next;
        while (cur != null) {
            if (cur.value == val) {
                pre.next = cur.next;
                break;
            }
            pre = cur;
            cur = cur.next;
        }
    }
    // 插入
    insert (node, pos) {
        // 边界检测
        let length = this.size(),
            cur = this.head;
        if (pos <= 0 || pos > (length + 1)) return;
        switch (pos) {
            case 1 : {
                node.next = cur;
                this.head = node;
                break;
            }
            case (length + 1) : {
                this.append(node);
                break;
            }
            default : {
                let pre;
                while (--pos) {
                    pre = cur;
                    cur = cur.next;
                }
                pre.next = node;
                node.next = cur;
            }
        }
    } 

    // 反转
    reverse () {
        if (this.isEmpty()) return;
        let cur = this.head,
            pre = null,
            next = null;

        while (cur.next != null) {
            next = cur.next;
            cur.next = pre;
            pre = cur;
            cur = next;
        }

        cur.next = pre;
        this.head = cur;
    }

    // 快慢指针的运用:获取中间节点值
    median () {
        if (this.isEmpty()) return;
        let fast = this.head; // 快指针
        let slow = this.head; // 慢指针
        while (fast.next != null) {
            if (fast.next.next == null) break;
            fast = fast.next.next;
            slow = slow.next;
        }
        return slow;
    }
   
    // 不换行输出打印链表
    static print (list) {
        let cur = list.head,
            out = " ";
        while(cur != null){
            out += "->" + cur.value;
            cur = cur.next;                           
        }
        console.log(out);
    }

}
``` 

简单运行一下测试代码

```javascript
var arr = [1,2,3,4];
var list = new SingleLinkedList(arr);
SingleLinkedList.print(list);

// 添加
list.append(new Node(5));
SingleLinkedList.print(list);

// 删除
list.del(1);
SingleLinkedList.print(list);
list.del(4);
SingleLinkedList.print(list);

// 插入
list.insert(new Node(1), 1);
list.insert(new Node(4), 4);
list.insert(new Node(6), list.size() + 1);
SingleLinkedList.print(list);

// 反转
list.reverse();
SingleLinkedList.print(list);

// 中间值
list.reverse();
SingleLinkedList.print(list);
console.log(list.median().value);
list.append(new Node(7));
SingleLinkedList.print(list);
console.log(list.median().value);
```

## 5. 双向链表的代码实现

首先是节点的定义。

```javascript
class Node {
    constructor (val) {
        this.value = val;
        this.next = null;
    }
    get next () {
        return this._next;
    }
    set next (next ){
        this._next = next; 
    }
    get pre () {
    	return this._pre;
    }
    set pre (pre) {
    	this._pre = pre;
    }
    get value () {
        return this._value;
    }
    set value (value) {
        this._value = value;
    }
}
```

双向链表的基本操作的实现与单向链表有所不同。

```javascript
class DoubleLinkedList {
    // 方便起见，使用一个数组来初始化一个链表
    constructor (arr) {
       for (let i = 0; i < arr.length; i++) {
          this.append(new Node(arr[i])); 
       } 
    }
    get head () {
        return this._head;
    }
    set head (head) {
        this._head = head;
    }

    // 定义基础操作，即成员方法

    // 判断是否为空
    isEmpty () {
        return (this.head == null);
    }
    // 返回节点的个数
    size () {
        let count = 0, cur = this.head;
        while (cur != null) {
            count += 1;
            cur = cur.next;
        }
        return count;
    }
    // 添加操作，头结点存储值·
    append (node) {
        let cur = this.head;
        if (this.isEmpty()) {
            this.head = node;
            return;
        }
        while(cur.next != null) {
            cur = cur.next;
        }
        cur.next = node;
        node.pre = cur;
    }
    // 删除
    del (val) {
        if (this.head.value == val) {
            this.head = this.head.next;
            this.head.pre = null;
            return;
        }
        let pre = this.head,
            cur = pre.next;
        while (cur != null) {
            if (cur.value == val) {
                pre.next = cur.next;
                cur.next.pre = pre;
                break;
            }
            pre = cur;
            cur = cur.next;
        }
    }
    // 插入
    insert (node, pos) {
        // 边界检测
        let length = this.size(),
            cur = this.head;
        if (pos <= 0 || pos > (length + 1)) return;
        switch (pos) {
            case 1 : {
                node.next = cur;
                cur.pre = node;
                this.head = node;
                break;
            }
            case (length + 1) : {
                this.append(node);
                break;
            }
            default : {
                let pre;
                while (--pos) {
                    pre = cur;
                    cur = cur.next;
                }
                pre.next = node;
                node.pre = pre;
                node.next = cur;
                cur.pre = node;
            }
        }
    } 

    // 反转
    reverse () {
    	if (this.isEmpty()) return;
        let cur = this.head,
            pre = null,
            next = null;
		while (cur.next != null) {
			next = cur.next;
			cur.next = pre;
			cur.pre = next;
			pre = cur;
			cur = next;
		}           

        cur.next = cur.pre;
        cur.pre = null;
        this.head = cur;
    }

    // 快慢指针的运用:获取中间节点值
    median () {
        if (this.isEmpty()) return;
        let fast = this.head; // 快指针
        let slow = this.head; // 慢指针
        while (fast.next != null) {
            if (fast.next.next == null) break;
            fast = fast.next.next;
            slow = slow.next;
        }
        return slow;
    }
   
    // 不换行输出打印链表
    static print (list) {
        let cur = list.head,
            out = " ";
        while(cur != null){
            out += "->" + cur.value;
            cur = cur.next;                           
        }
        console.log(out);
    }
}
```

简单的测试代码

```javascript
var arr = [1,2,3,4];
var list = new DoubleLinkedList(arr);
DoubleLinkedList.print(list);

// 添加
list.append(new Node(5));
DoubleLinkedList.print(list);

// 删除
list.del(1);
DoubleLinkedList.print(list);
list.del(4);
DoubleLinkedList.print(list);

// 插入
list.insert(new Node(1), 1);
list.insert(new Node(4), 4);
list.insert(new Node(6), list.size() + 1);
DoubleLinkedList.print(list);

// 反转
list.reverse();
DoubleLinkedList.print(list);

// 中间值
list.reverse();
DoubleLinkedList.print(list);
console.log(list.median().value);
list.append(new Node(7));
DoubleLinkedList.print(list);
console.log(list.median().value);
```

## 参考

> 算法参考：http://algorithm.yuanbin.me/zh-hans/ <br>

> 实现参考：https://github.com/LukeLin/data-structure-with-js