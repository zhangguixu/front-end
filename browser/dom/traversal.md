# 文档结构与遍历

## 1. 文档结构

文档中所有的节点之间都存在这样或那样的关系。节点间的各种关系可以用传统的家族关系来描述，相当于把文档树比喻成家谱。

![节点关系](../../image/node-relation.png)

节点的关系属性:

1. 子元素

    * childNodes 返回NodeList对象
    * firstChild / firstElementChild
    * lastChild / lastElementChidl
    * childElementCount

2. 父节点: parentNode

3. 兄弟节点，返回的是NodeList

    * previousSibling / previousElementSibling
    * nextSibling / nextElementSibling

## 2. 遍历

### 2.1 获取兄弟节点

查找前一个兄弟的元素

```javascript
function prev(e) {
    do {
        e = e.previousSibling;
    } while (e && e.nodeType !== 1);
    return e;
}
```

查找下一个兄弟元素

```javascript
function next(e) {
    do {
        e = e.nextSibling;
    } while (e && e.nodeType !== 1);
    return e;
}
```

### 2.2 获取子节点

获取第一个子节点

```javascript
function first(e) {
    e = e.firstChild;
    return e && e.nodeType !== 1 ? next(e) : e;
}
```

获取最后一个子节点

```javascript
function last(e) {
    e = e.lastChild;
    return e && e.nodeType !== 1 ? pre(e) : e;
}
```

### 2.3 获取父节点

文本节点是没有子节点，因此不用判断父节点的类型。

```javascript
function parent(e, n) {
    n = n || 1;
    for (var i = 0; i < n; i++){
        if(e) e = e.parentNode;
        return e;
    }
}
```

## 参考

1. 《JavaScript权威指南》
2. 《精通JavaScript》
