# 常见的标签

## 文本

1. h1~h6：headline，表示标题，一般来说h1的权重最高，最好页面中不要有太多。
2. p：paragraph，表示一个文字段落
3. strong：文字加粗
4. em：emphasize，斜体，表示强调该内容

## 无实义元素

1. div：用来组合文档中的块级元素
2. span：被用来组合文档中的行内元素。

## 列表

1. ul + li：无序列表
2. ol + li：ordered list，有序列表
3. dl + dt + dd：definition list，定义列表，其中dt定义了列表中的项目，dd描述列表中的项目。

## 表单

```html
<form name="" method="POST" action="" enctype="multipart/form-data">
    <input type="text" name="">输入文本
    <input type="file" name="">上传文件
    <input type="password" name="">密码
    <input type="checkbox" name="">复选框
    <input type="radio" name="">单选按钮
    <input type="submit" name="">提交按钮
    <input type="reset" name="">重置
    <select>
        <option value="1">1</option>
        <option value="2">2</option>
    </select>
</form>
```

## 表格

1. caption:表格标题
2. thead:表格的表头
3. tbody:表格中的主体内容
4. tfoot:表格中的页脚内容
5. tr:表格中的一行
6. th:表头单元格，包含表头信息
7. td:标准单元格，包含数据

```html
<table>
    <caption>表格标题</caption>
    <thead>
        <tr>
            <th>表头1</th>
            <th>表头2</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>内容1</td>
            <td>内容2</td>
        </tr>
    </tbody>
    <tfoot>
        <tr>
            <td>页脚1</td>
            <td>页脚2</td>
        </tr>
    </tfoot>
</table>
```

## 图片

img：向网页中嵌入一幅图像，注意两个属性src（链接）和alt（图像的替代文本）

```html
<img src="" alt="">
```

## 超链接

a标签定义超链接，最重要的属性是href，指示链接的目标，在所有浏览器中，链接的默认外观是

1. 未被访问的链接带有下划线而且是蓝色的
2. 已被访问的链接带有下划线而且是紫色的
3. 活动链接带有下划线而且是红色的

## html5新增语义标签

### 多媒体内嵌

1. audio：音频资源
2. video：视频资源
3. canvas：图形容器，可以加载图像或者使用脚本绘制图形

### 标题

hgroup:对标题进行组合

### 结构

1. header：文档的页眉
2. nav：导航链接
3. aside：侧栏
4. section：文档中的节（section、区段）。比如章节、页眉、页脚或文档中的其他部分
5. article：一篇文章
6. footer：文档的页脚



