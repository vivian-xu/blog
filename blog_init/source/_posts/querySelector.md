---
title: querySelector
categories:
- 编程
tags:
- DOM
- 抄抄抄
date: 2016-12-03
---

最近, 看到别人使用 querySelector, 而我之前根本没听过。所以找到 MDN 上的资料

### 简单知识介绍

- DOM 扩展

它属于 DOM 扩展的部分。

对 DOM 的两个主要的扩展是 Selectors API 和 HTML5，这两个扩展都源自开发社区, 而将某些常见做法及 API 标准化, 一直是众望所归。

<!-- more -->

此外还有一个不那么引人注目的 Element Traversal ( 元素遍历 ) 规范，为 DOM 添加了一些属性。

- Selectors API

是由 W3C 发起制定的一个标准, 致力于让浏览器原生支持 CSS 查询。之前所有实现着一功能的 JS 库，都会写一个基础的 CSS 解析器，然后再使用已有的 DOM 方法查询文档找到匹配的点。

而把这个功能变成原生 API 之后，解析和树的查询操作可以在浏览器内部通过编译后的代码来完成，极大的改善了性能。

Selectors API Level 1 的核心是两个方法：
__querySelector()__ 和 __querySelectorAll()__， 可以通过 Document 及 ELement 类型的实例调用它们。

已完全支持的浏览器 IE8+, Firefox 3.5+, Safari 3.1+, Chrome 和 Opera 10+

### document.querySelector /  Element.querySelector()

- 语法： element = document.querySelector( selectors )
-  selectors: 是一个字符串，包含一个或是多个 css 选择器，多个以逗号隔开。

比如: 找到 div 标签中的 input 标签。

```
<div class="user-panel main">
    <input name="login"/>
</div>
```

```
var el = document.querySelector("div.user-panel.main input[name=login]");
```
- element 是 __一个__ element 对象 ( DOM 元素 )

- 如果没有找到匹配的元素，则返回 null。

- 找到多个，返回第一个匹配到的元素。

- Css 伪类不会返回任何元素。

- 如果要匹配的ID或选择器不符合 CSS 语法（比如不恰当地使用了冒号或者空格），你必须用反斜杠将这些字符转义。由于 JavaScript 中，反斜杠是转义字符，所以当你输入一个文本串时，你必须将它转义两次（一次是为 JavaScript 字符串转义，另一次是为 querySelector 转义）

比如: 匹配 id 为  foo\bar 的DOM

```
document.querySelector('#foo\\\\bar');

```

#### document.querySelectorAll ( Element.querySelectorAll() )

- elementList = document.querySelectorAll(Selectors);

- elementList 是一个 __non-live__ 的 NodeList 类型的对象.  ( 静态的 )

- selectors 是一个由逗号连接的包含一个或多个CSS选择器的字符串.

比如: 返回一个文档中所有的 class 为 "note" / "alert" 的 div 元素。

```
var matches = document.querySelectorAll("div.note, div.alert");
```

- WebKit 内核的浏览器有一个 __bug__: 如果 selectors参数中包含 CSS伪类, 返回的elementList 包含一个<html> 元素,而不是空的.

---
 **NOTE!!**

element.querySelector / element.querySelectorAll
    查找的时候都包含 element
----

{% blockquote %}

参考链接：

[MDN: NodeList](https://developer.mozilla.org/zh-CN/docs/Web/API/NodeList)

[MDN: Document.querySelectorAll](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/querySelectorAll)

[MDN: Document.querySelector](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/querySelector)


_JS高级程序设计 ( 第3版 )_

{% endblockquote %}
