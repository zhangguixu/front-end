# 选择器

## 1. id

任何HTML元素可以有一个id属性，在文档中该值必须唯一，即同一个文档中两个元素不能有相同的ID（其实不一定，可以有，只是不符合规范，而且会可能导致你获取不到自己的想要的元素）。

```javascript
var el = document.getElementById('id');
```

这个API在低于IE8版本的浏览器中，对匹配元素的ID不区分大小写，而且也返回匹配`name`属性的元素。

## 2. name

HTML的name属性是为表单元素分配名字，在表单数据提交到服务器时使用该属性的值。

name属性的值不是必须唯一的，多个元素可能有同样的名字，例如表单中的单选和复选按钮。

```javascript
var el = document.getElementsByName('name');
```

为了兼容，应该注意不要将同样的字符串同时做名字和ID

## 3. tagName

```javascript
// NodeList对象
var spans = document.getElementsByTagName("span"); 
```

## 4. className

HTML元素的class属性值是一个以空格隔开的列表，可以为空或包含多个标识符。在JavaScript中class是保留字，所以客户端JavaScript使用className属性来保存HTML的class属性值。

```javascript
// NodeList对象
var warnings = document.getElementsByClassName("warnings");
```

*兼容性：IE8+*

## 5. css选择器

CSS样式表有一种非常强大的语法，那就是选择器，它用来描述文档中的若干或多组元素。querSelectorAll\querySelector可以来接收CSS选择器，返回选取的元素。

* querySelectorAll: 返回NodeList对象，即符合描述的所有元素，这里需要注意的是，返回的NodeList对象不是实时的了，而是返回调用时刻所匹配的元素。
* querySelector: 返回的第一个匹配的元素（以文档顺序）

所有当前的浏览器都支持querySelector()和querySelectorAll()方法，但是注意的，当前的浏览器除了IE都支持CSS3选择器，IE7和8只支持CSS2选择器。

```javascript
var id = document.querySelector("#id");
```

## 7. 快捷选择

1. body

    ```javascript
    var body = document.body;
    ```

2. head，有兼容性问题

    ```javascript
    var head = document.head;// HTML 
    ```

3. form

    ```javascript
    var forms = document.forms;// HTMLCollection
    ```

4. link

    ```javascript
    var links = document.links;// HTMLCollection
    ```

5. image

     ```javascript
    var images = document.images;// HTMLCollection
    ```

## 8. document.all[]

在DOM标准化之前，IE 4引入了document.all[]集合来表示文档中的所有元素（除了Text节点），如今这个方法已经被废弃了，不应该再使用了，仅作了解。

```javascript
document.all[0] // 文档中的第一个元素
document.all['navbar'] // id或name为navbar的元素
document.all.tags('p')[0] // 文档中第一个<p>元素
```