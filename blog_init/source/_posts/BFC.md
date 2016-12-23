---
title: BFC ( Block Formatting Context )
categories:
- 编程
tags:
- 抄抄抄
- CSS 布局
- BFC
auto_spacing: true
---

#### 表现

- 在创建了 Block Formatting Context 的元素中，

 垂直方向上：其子元素会一个接一个地放置，他们的起点是一个 BFC 的顶部，两个相邻元素的块级元素的垂直边距会 __折叠__。
  水平方向上：每一个盒子的左外边缘 ( margin-left ) 会触碰到容器的左边缘 ( border-left )（ 从右到左则是右边缘 ），即使存在浮动也是如此，除非这个元素也创建了一个新
  的 BFC
- BFC 元素的布局是不受外界的影响，容器里面的子元素也不会影响到外面的元素的 ( 常用此来清除浮动 )

- 计算高度的时候，浮动元素也会算在其中

#### 创建 BFC

Floats, absolutely positioned elements, block containers that are not block  boxes,
block boxes with 'overflow' oter than 'visible' establish new block formatting contexts for their __contents__.

- 根元素或其它包含它的元素
- 浮动
- 绝对定位  ( position 为 absolute 或 fixed)
- 非块级盒子的块级容器。 ( inline-block, table-cell, table-captions )
- overflow 不是 visible 的块极盒子。
- 弹性盒子 flex boxes (元素的 display: flex 或 inline-flex)


<!-- more -->

#### 利用 BFC

- 清除浮动

```
.clearfix {
    *zoom: 1;
}
.clearfix:after {
    content: '';
    display: table;
    clear: both;
}
```

- float ( 左边 ) + padding／margin ( 在 float 元素上 )  + BFC ( 右边 ) 实现自适应布局，无需担心元素宽度

    - overflow:auto/hidden  IE7+
    - display: inline-block  IE6/IE7
    - display: table-cell  IE8+

- 两栏／多栏自适应布局的通用类语句 ( block 水平的标签 ！ 需要配合 浮动 ) ( 要清除浮动哦 )

```
.cell {
    display: table-cell;
    width: 9999px;
    *display: inline-block;
    *width: auto;
}
```


{% blockquote %}

参考：

[张鑫旭:CSS深入理解流体特性和BFC特性下多栏自适应布局](http://www.zhangxinxu.com/wordpress/2015/02/css-deep-understand-flow-bfc-column-two-auto-layout/)

[W3help:KB006: CSS 框模型( Box module )](http://w3help.org/zh-cn/kb/006/)

[KB010: 常规流( Normal flow )](http://w3help.org/zh-cn/kb/010/)

{% endblockquote %}
