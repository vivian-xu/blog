---
title: this
categories:
- 编程
tags:
- 吵吵吵
- JS 基础
auto_spacing: true
---

this 是个坑，学习一下，期待以后掉坑的几率越来越小～

#### 基本知识

- js中严格模式和非严格模式中的 this 会有所不同

- 大部分情况下，函数的调用方式决定了 this 的值。

- this在执行中不能被赋值，同一个函数会因为不同的调用而导致 this 的不同

- bind 方法可以直接设置 this 而不用考虑如何被调用

<!-- more  -->

#### this 在全局上下文中

在全局上下文中（任何函数体外），this 指代全局对象。

```
console.log(this.document === document); // true

// 在浏览器中，全局对象为 window 对象：
console.log(this === window); // true

this.a = 37;
console.log(window.a); // 37
```
#### 函数上下文中

取决于函数如何被调用

- 直接调用

在严格模式下，this 是在进入运行环境的时候设置的，若没有设置，则 this 维持 undefined, 同时它还能设置成任意值，比如 null, 3, 'hey'。

```
function f1() {
  return this;
}

f1() === window; // true, 严格模式下为 undefined
```

- 作为方法被调用

  则指向调用该函数的对象 ( 离得最近的 . 之前的对象 ）

```
var o = {
  prop: 37,
  f: function() {
    return this.prop;
  }
};

console.log(o.f());  // 37
```

只指向直接调用它的对象

```
o.b = {
  g: independent,
  prop: 42
};
console.log(o.b.g()); // logs 42 指向 o.b
```

无论在何处如何定义函数都不会影响到 this 的指向行为

```
var o = {prop: 37};
function independent() {
  return this.prop;
}
o.f = independent;
console.log(o.f()); //37
```

- 原型链上的 this

如果该方法存在于一个对象的原型链上，那么 this 依旧是指向 调用这个函数的 对象。
```
var o = {
  f: function() {
    return this.a + this.b;
  }
}

var p = Object.create(o);
p.a = 1;
p.b = 3;

console.log(p.f()); // 4
```
在这个例子里，o 是 p 的原型，p自身 是没有属于自己的 f 方法的，它的 f 方法继承于它的 原型 o, 查找的过程是从 p.f 开始的，所以，this 就指向 p

- getter 和 setter 中的 this

当函数做为 getter setter 调用的时候，this 会绑定到设置属性 ／ 得到属性的对象。

```
function modulus(){
  return Math.sqrt(this.re * this.re + this.im * this.im);
}


var o = {
  re: 1,
  im: -1,
  get phase(){
    return Math.atan2(this.im, this.re);
  }
};

Object.definePorperty(o, 'modulus', {
get: modulus,
enumerable:true,
configurable: true
} )

console.log(o.phase, o.modulus ); // logs -0.78 1.4142

```
- 构造函数中的 this

当一个函数被作为构造来使用 ( 使用 new 关键字 ), this 被绑定在即将创建的新对象。

__NOTE: 当构造函数里有 return 语句并且 return 一个 Object 的时候，返回这个 Object, 否则就返回 this 绑定的结果( envRec.GetThisBinding() ) __

```
function C(){
  this.a = 37;
}

var o = new C();
console.log(o.a); // 37

function C2(){
  this.a = 37;
  return {a:38};
}
o = new C2();
console.log(o.a); // 38
```
- call 和 apply

当一个函数的函数体中使用了 this 关键字，用 call, 或者 apply 方法调用函数的时候，this 的值绑定到一个指定的对象

```
function add(c, d){
  return this.a + this.b + c + d;
}

var o = {a:1, b:3};

add.call(o, 5, 7); // 1 + 3 + 5 + 7 = 16

add.apply(o, [10, 20]); // 1 + 3 + 10 + 20 = 34
```

使用 call / apply 时，如果指定的 this 不是一个  object 类型，则会使用相应的包装类型函数 将它们包装成 object.

```
function bar() {
  console.log(Object.prototype.toString.call(this));
}

bar.call(7); // [object Number] ,  7 被 使用 new Number(7) 包装
```
- bind 方法
使用 Function.prototype.bind 方法绑定 this 到第一个参数上， 无论这个函数时何处何时调用的， this 的指向不变

```
function f(){
  return this.a;
}

var g = f.bind({a:"azerty"}); // 将 f 的 this 绑定在 {a: "azerty"} 上
console.log(g()); // azerty

var o = {a:37, f:f, g:g};
console.log(o.f(), o.g()); // 37, azerty
```

- Dom事件处理函数中的 this

当函数被作为 事件处理函数的时候 this 指向触发该事件的元素( e.currentTarget)

```
// 被调用时，将关联的元素变成蓝色
function bluify(e){
  console.log(this === e.currentTarget); // 总是 true

  // 当 currentTarget 和 target 是同一个对象是为 true
  console.log(this === e.target);
  this.style.backgroundColor = '#A5D9F3';
}

// 获取文档中的所有元素的列表
var elements = document.getElementsByTagName('*');

// 将bluify作为元素的点击监听函数，当元素被点击时，就会变成蓝色
for(var i=0 ; i<elements.length ; i++){
  elements[i].addEventListener('click', bluify, false);
```

- 内联事件的处理函数

 this 指向监听器所在的dom 元素
 ```
 <button onclick="alert(this.tagName.toLowerCase());">
   Show this
 </button>
 ```
下面这个样子的 this 会指向全局对象
 ```
 <button onclick="alert((function(){return this})());">
   Show inner this
 </button>
 ```

{% blockquote %}

参考链接：

[MDN: this](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this)

_JS高级程序设计 ( 第3版 )_

{% endblockquote %}

