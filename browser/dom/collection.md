# 元素集合对象

在DOM中经常会接触到的集合对象，因此在这里做一个汇总

## 1. NodeList

NodeList是一种类数组对象，用于保存一组有序的节点。它的独特指出在于，它实际上是`基于DOM结构动态执行查询的结果`，因此DOM结构的变化能够自动反映在NodeList对象中。

*下列这段代码能够加深对NodeList的认识，运行在浏览器可能会导致死机*

```javascript
var alldivs=document.getElementsByTagName('div');
//死循环，alldivs.length不断增加
for(var i= 0; i < alldivs.length;i++){
    console.log(alldivs.length);
    document.body.appendChild(document.createElement('div'));
}
```

通常出于性能和操作方面的考虑，会将NodeList转化为数组

由于IE8及更早版本将NodeList实现为一个COM对象，因此只能手动枚举所有成员。其他的版本或其他浏览器，则使用Array.prototype.slice进行转换就可以。

```javascript

function convertToArray(nodes){
    var array = null;
    try{
        array = [].prototype.slice.call(nodes,0);
    } catch (ex){
        array = [];
        for (var i = 0,len = nodes.length;i++){
            array.push(nodes[i]);
        }
    }
    return array;
}
```

## 2. HTMLCollection

与NodeList类似，是一个"动态"的集合，原型属性有

* length ： 对象数量
* item(index) : nodeList[index]
* nameItem(name) : 根据name获取对象

## 3. NamedNodeMap

与NodeList类似，是一个"动态"的集合，`nodeName`是属性的名称，`nodeValue`是属性的值，原型属性有

* getNamedItem(name) : 返回nodeName属性等于name的节点
* removeNamedItem(name) : 移除nodeName属性等于name的节点
* setNamedItem(node) : 添加节点，以节点的nodeName属性为索引
* item(pos) : 返回pos位置处的节点