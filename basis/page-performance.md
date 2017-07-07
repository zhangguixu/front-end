# 页面生成基本过程

页面生成过程，大致可以分成五步

1. HTML代码转化为DOM

2. CSS代码转化成CSSOM（CSS Object Model）

3. 结合DOM和CSSOM，生成一棵渲染树（包含每个节点的视觉信息）

4. 生成布局（layout），即将所有渲染树的所有节点进行平面合成

5. 将布局绘制（paint）在屏幕上

![网页生成具体流程](../image/web-page-parsing.png)

在这五步里面，第一步到第三部都非常快，`第四步和第五步很耗时`。



