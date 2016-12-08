---
title: nodeList
categories:
- 编程
tags:
- DOM
- 抄抄抄
date: 2016-12-03
---

跟 querySelector 一块查到的 nodeList

### 基本定义

NodeList 对象是一个节点的集合，是由 __Node.childNodes__ 和 __document.querySelectorAll__ 返回的. (不是 Array)

### 属性

- length


### 方法: item( idx )

- 返回NodeList对象中指定索引的节点,如果索引越界,则返回null.等价的写法是nodeList[idx], 不过这种情况下越界访问将返回undefined.

<!-- more -->

### 其他

- 大多数情况下，NodeList 对象都是个实时集合。意思是说，如果文档中的节点树发生变化，则已经存在的 NodeList 对象也可能会变化。

  + Node.childNodes 是实时的

  + document.querySelectorAll 返回一个静态的 NodeList

-  NodeList 不是数组:

  + NodeList的原型链是这样的：

  myNodeList --> NodeList.prototype --> Object.prototype --> null


### 遍历一个 NodeList 对象的所有节点。

 +  for 循环

```
for (var i = 0; i < myNodeList.length; ++i) {
    var item = myNodeList[i];  // 调用 myNodeList.item(i) 是没有必要的
}
```
+ 在支持 for...of 的浏览器中 for...of 循环将会正确的遍历 NodeList 对象：

```
var list = document.querySelectorAll( 'input[type=checkbox]' );
for (var item of list) {
    item.checked = true;
}
```

+ 将 NodeList 转换为 Array ( 类数组的方式 )

    ---类数组： 有length 属性

```
var div_list = document.querySelectorAll('div'); // 返回 NodeList
var div_array = Array.prototype.slice.call(div_list); // 将 NodeList 转换为数组

//更好的写法
[].forEach.call(Nodelist,function(){})
```

__NOTE:__

最好不要使用 for...in 或者 for each...in 。__

- 它们会把 length 和 item 属性也遍历出来。

- 而 for in 也不保证顺序


{% blockquote %}

参考链接:

[MDN: NodeList](https://developer.mozilla.org/zh-CN/docs/Web/API/NodeList)

{% endblockquote %}

