# 属性

属性的操作无非就是

1. 获取属性
2. 设置属性/添加
3. 删除属性

## 1. getAttribute()

```html
<div id="myDiv" class="bd" data-my-attribute="hello"></div>
<script type="text/javascript">
    var div = document.getElementById('myDiv');
    div.getAttribute('id');//myDiv
    div.getAttribute('class');//bd
    div.getAttribute('data-my-attribute');//hello
</script>
```

根据HTML5规范，自定义特性应该加上data-前缀以便验证，此外，只有非自定义的属性才会以属性的形式添加到DOM对象中

```javascript
div.data-my-attribute; //undefined(IE除外)
```

有两类特殊的属性，虽然有对应属性名，但是属性的值与通过getAttribute()返回的值并不相同。

1. `style`:通过属性来访问返回一个对象，而通过getAttribute()返回CSS文本。
2. `onXXX`:例如onclick这样的事件处理程序，通过属性访问一个JavaScript函数，而通过getAttribute()返回相应的代码

*一般来说，只有在取得自定义特性值的情况下，才会使用getAttribute()*

## 2. setAttribute()

既可以操作HTML属性，也可以操作自定义属性

```javascript
div.setAttribute('id','myDiv');
div.setAttribute('data-my-attribute','hello');
```

也可以直接给属性赋值设置属性的值，但是如果为DOM添加的是自定义的属性，该属性不会自动成为元素的特性

```javascript
//添加非自定义属性【成功】
div.id = 'myDiv';

//添加自定义属性，【不可行】
div.mycolor = 'red';
div.getAttribute(mycolor); //null(IE除外)
```

## 3. removeAttribute()

不常用，但在序列化DOM元素时，可以通过它来确切地指定要包含哪些属性？

## 4. attributes属性

Element类型是使用attributes属性唯一一个DOM节点类型。attributes属性中包含一个`NamedNodeMap`。元素的每一个属性都由一个Attr节点表示，每个节点都保存在NamedNodeMap对象中

```javascript
//取得元素的id属性值
var id1 = element.attributes.getNamedItem('id').nodeValue;
var id2 = element.attributes['id'].nodeValue;

//设置元素的属性的值
element.attributes['id'].nodeValue = 'myDiv';

//删除属性
var oldAttr = element.attributes.removeNamedItem('id');
```

*一般来说，由于attributes的方法不够方便，还是会较多使用前面getAttribute(),setAttribute(),removeAttribute()*

在需要将`DOM结构序列化为XML或HTML字符串时`，就可以用到attributes属性

在这里需要注意两个问题

1. 针对attributes 对象中的特性，不同浏览器返回的顺序不同。这些特性在XML或HTML代码中出现的先后顺序，不一定与它们出现在attributes 对象中的顺序一致。

2. IE7 及更早的版本会返回HTML元素中所有可能的特性，包括没有指定的特性。换句话说，返回100多个特性的情况会很常见。

```javascript
function outputAttribtues(element){
    var pairs = [],
        attrName,
        attrValue,
        i,
        len;

    for(i = 0,len = element.attributes.length;i < len;i++){
        attrName = element.attributes[i].nodeName;
        attrValue = element.attributes[i].nodeValue;
        /*
            针对IE7及更早版本中存在的问题，可以利用每个属性节点的specified
            属性，如果这个属性的值为true，说明要么是在HTML中指定了相应属性
            ，要么是通过了setAttribute()方法设置了该属性。在IE中，所有未设
            置过的属性的该属性值为false。当然其他浏览器不会有问题2的存在，
            所以无需考虑(在其他浏览器，任何属性节点的specified值始终是(
            true)
        */
        if(element.attributes[i].specified){
            pairs.push(attrName + '=\"' + attrValue + '\"');
        }
    }
    return pairs.join(' ');
}
```

## 参考

1. 《JavaScript高级程序设计》