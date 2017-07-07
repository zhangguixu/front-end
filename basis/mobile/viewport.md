# viewport

## 1. 概念

移动端的页面会经过两层的处理

1. 页面渲染在一个viewport上，先保证排版正确。
2. 对页面进行缩放

## 2. viewport分类

1. layout viewport：底层，无法控制
2. visual viewport：上层，窗口缩放scale

## 3.  实践方案

1. 方案1：根据设备的实际宽度来设计（常用）
2. 方案2：1px = 1dp，缩放0.5，这样子1px边框和高清图片都不需要处理

## 4.  最佳实践

```html
<meta name="viewport" content="width=device-width,initial-scale=1
                                user-scalable=no">
```