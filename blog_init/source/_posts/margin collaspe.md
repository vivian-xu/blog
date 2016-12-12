---
title: 折叠外边距 ( Collapsing margins )
categories:
- 编程
tags:
- 抄抄抄
- CSS 布局
- margin
auto_spacing: true
---

大概一年前看过的知识，如今已经忘得一干二净，重新再来一遍！

#### 基本定义

在 CSS, __毗邻__ 的 2+ 个盒子 ( 不一定是兄弟元素 ) 的 margins 会合并成一个 Margin。Margins 的这种合并行为被称作 collapse, 产生的这个合并了的外边距被称为折叠外边距 (collapsed margin)。


<!-- more -->

#### 什么是相邻的盒子 ( 两点 )

- 这两个或多个外边距没有被非空内容、padding、border 或 clear 分隔开 ( 形成间隙 )。( 一种是嵌套，一种是平行 )

- 这些 margin 都处于 __普通流__中。 ( 1. 非浮动元素 2. 非绝对定位元素 )


如下图：

  - Box-1 , Box-2 嵌套，相邻
  - Box-1 , Box-3 平行，相邻

![](http://img.jackon.me/margins1.png)

html 代码
```
// html 代码
// Box-1 , Box-2 嵌套
<div style="width: 150px; height: 150px; border: 5px solid red; border-top: none; margin: 40px 0; background: blue; color: #fff; font-size: 20px;" >
  <div style=" width: 100px; height: 100px; background: yellow; margin-top: 15px; color: #000;" >
    Box-2
  </div>
  Box-1
</div>

// Box-1 Box-3 平行
<div style="width: 100px; height: 100px; background-color: lightblue; margin: 40px 0; font-size: 20px;" >
  Box-3
</div>
```

**NOTE: clearance  ( 间隙 )**

当浮动元素之后的元素设置clear以闭合相关方向的浮动时，根据w3c规范规定，闭合浮动的元素会在其margin-top以上产生一定的空隙（clearance），该空隙会阻止元素margin-top的折叠，并作为间距存在于元素的margin-top的上方。

--- 



#### 哪些会发生 margin 折叠

- 元素自身的 __margin-bottom__ 和 __margin-top__ __相邻__时也会折叠

- __两个或多个 毗邻的普通流中__的 __块元素垂直方向__上的 margin 会折叠

  要点：

  + 两个或多个 （数量必须是大于一）

  + 必须是块元素

  + 只能是垂直方向

NOTE: 在没有被分隔开的情况下，一个元素的 margin-top 会和它普通流中的第一个子元素(非浮动元素等)的 margin-top 相邻； 只有在父元素的 height 是 "__auto__" 的情况下，它的 margin-bottom 才会和它普通流中的最后一个子元素(非浮动元素等)的 margin-bottom 相邻。

#### 哪些不会发生 margin 折叠

- 创建 BFC 的元素，不与其子元素发生 margin 折叠

- 浮动元素，绝对定位元素不与任何元素发生 margin 折叠 ( 不在普通流，创建 BFC )

- inline-block 元素，不与任何元素发生 margin 折叠  ( 不是 块级盒子  block-level box )

#### 结果

- 两个相邻的外边距都是正数时，折叠结果是它们两者之间较大的值。 (  __+ +__  取大)

- 两个相邻的外边距都是负数时，折叠结果是两者绝对值的较大值。 ( __- -__  取绝对值大的 )

- 两个外边距一正一负时，折叠结果是两者的相加的和。( - + 相加 )


{% blockquote %}

参考：

[W3help:KB006: CSS 框模型( Box module )](http://w3help.org/zh-cn/kb/006/)

[KB010: 常规流( Normal flow )](http://w3help.org/zh-cn/kb/010/)

{% endblockquote %}
