---
title: 原型
categories:
- 编程
tags:
- 抄抄抄
- 总结
- 原型
- prototype
- JS 基础
---

这块好难理解，
从最基础的开始,
为了便于自己以后还能想起来，多写了很多废话

###  构造函数，原型，实例

- 构造函数都有一个 __原型对象( 原型 )__ prototype

- 构造函数的 原型对象 ( prototype ) 里，都包含了一个指向 __构造函数__的指针 ( constructor )

- 由于每个对象都有一原型， prototype 也有一个指向它的原型的指针( `__proto__` )

- 实例都包含了一个指向 __原型对象( prototype )内部__的指针 (`__proto__`)。


<!-- more  -->

_**NOTE**_:
constructor:
为了让每个对象知道 创造它的构造函数是谁，所以每一个 构造函数的 原型对象( prototype , 继承下去的 )  上都有一个 constructor 指向 它( 构造函数  ) 自己。( 心机 )


下来看下实验

```
// 构造函数 createA
// createA 的原型对象( prototype ) 有一个属性是 constructor,  constructor 是指向 createA 的

function createA() {}

console.log('function createA()');
console.log( `createA.prototype: ${createA.prototype}` );
console.log( `createA.prototype.constructor: ${createA.prototype.constructor}` );

// objectA 是 createA 的一个实例
// objectA 有一个 __proto__ 属性是指向 objectA.prototype ( 构造函数的原型对象 ) __内部__ 的

let objectA = new createA();

// objectB 也有一个 __proto__ 属性，指向哪里呢？

let objectB = objectA;

console.log('objectA');
console.log('objectB');

console.log( `objectA: ${objectA}` );
console.log( `objectB: ${objectB}` );

console.log(`objectA.__proto__: ${objectA.__proto__}`);
console.log( `objectB.__proto__: ${objectB.__proto__}` );

console.log(`objectA.__proto__.constructor: ${objectA.__proto__.constructor}`);
console.log( `objectB.__proto__.constructor: ${objectB.__proto__.constructor}` );
```

结果:
![](http://img.jackon.me/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-12-16%20%E4%B8%8A%E5%8D%881.48.19.png)

也就是说：

`objectA = objectB`, A 就是 B 就是 A, 所以  __构造函数__ 之下就只有一层, 也就是 构造函数 这个 _爸爸/ 妈妈_ __只有__ 儿女，_没有_ 孙子 / 孙女 或者  孙孙子 / 孙孙女 。。。。之类


那么，__构造函数 的原型对象__ (createA.prototype) 也是一个 _对象_，那么 这个_对象_的 原型 ( `createA.prototype.__proto__` ) 是什么？
如果把它 (构造函数 的原型对象) 看成 实例的话，那么 它的 原型 (`__proto__`) 应该指向 它的 构造函数的原型对象 ( 也就是说 `createA.prototype.__proto__.constructor` `应该指向它的构造函数 ) 。那么这个构造函数是什么呢?

测试一下

```
function createA() {}

console.log('function createA()');
console.log( `createA.prototype: ${createA.prototype}` );

console.log( `createA.prototype.constructor: ${createA.prototype.constructor}` );

console.log( `createA.prototype.__proto__: ${createA.prototype.__proto__}` );

console.log( `createA.prototype.__proto__.constructor: ${createA.prototype.__proto__.constructor}` );

console.log( `createA.prototype.__proto__._proto__: ${createA.prototype.__proto__.__proto__}` );
```
结果：

![](http://img.jackon.me/proto_null.png)

看到结果想起来，每个 __函数__都是 __对象__，
所以 函数的 原型对象的 构造函数 ( `createA.prototype.__proto__.constructor`) 是 function object( ){ } ( JS 默认的所有对象的构造函数 ),
因为函数都有对象原型 (function object() {} 的 对象原型，也就是`createA.prototype.__proto__` ) ，
-所以 这个对象原型的 `__proto__` 是什么呢？
 答案是 __null__。JS 中所有的原型链的最终都是 __null__


#### 小小的总结一下

JS 数据类型分为两种, 一种是 __基本数据类型( 数值字面量, 不是 object )__。

第二种就是 __Object (复杂数据类型, 引用类型值，对象 ) 类型__。

而 Object 中包括了许多类型 ( Array, Function, RegExp )

而这些__复杂数据类型__ _每一个_ 都代表着_一个_ __构造函数__。
也就是说,
__每一个 数组 都是 Function Array ( Array 构造函数 )的实例，__ 每一个实例都是 它的构造函数的

每一个 __非 Function 类型的对象__ 的 __原型__ 都指向着 相应 的__构造函数的 原型对象__ ( 比如  `array.__prop__` --> Array.prototype ( function Array), `string.__proto__ --> Object.prototype (function Object)` )。

而 function 类型的原型对象 ( prototype ) 是 function Object。

而最终 __function Object__ 的原型对象 ( prototype ) 是 __null__

 ! ! NOTE:
 这里没有 函数类型的原型 --> function function (function 构造函数) 之说。

### 原型链

#### 概念

- 是实现 _继承__ 的主要方法
- 基本思想是利用 _原型_让一个 _引用类型_ __继承__另一个_引用类型_的 __属性和方法__。

- 假如，我们让 原型对象 等于 另一个类型的 _实例_，此时的原型对象将包含一个指向 另一个原型对象 (prototype) 的指针(`__proto__`)。相应的，另一个原型对象 ( __prototype__) 中也包含着一个指向另一个构造函数(__constructor__) 的指针。

👆的看得不是很懂。。。


上个星期写到了这里，写不下去了。。

昨天晚上向男票寻求帮助，感觉差不多了， 但是。。。 要先去面试，回来再补

----
update

#### 我的理解

原型链可以这样理解:

每一个对象都是一个 container , 这个 container 里面都是些 key:value ( 键值对 )。 container 分为两部分，__第一部分__ 是自身包含的属性，方法。__第二部分__ 就是一个指向原型的指针 (prototype / _prop_)。

而最重要的区别就是，寻找一个 key 的顺序。

当我们寻找一个 key 的时候。首先，程序会从这个对象 ( container ) 的第一部分找起, 如果第一部分没有，则到 __第二部分__，根据指针到它所指的地址的对象 ( 原型对象 ) 上找。同样，原型对象也是一个 container, 它的结构跟之前的对象结构完全一样，所以在它的第一部分找不到的时候，程序会到它的第二部分的指针所指的对象 ( 原型对象 ) 上去找，而这个对象与前两个的结构也完全一样...... 就这样不停的寻找，直到找到寻找的 key, 完。

而在这个寻找的过程中, 一个对象与另一个对象相连，形成的 __链__。
而，基本上每一个 container 的第二部分的指针都是指向 __它的原型对象__ ( _这个对象往往也是 构造它自己的__构造函数__的 __原型对象___ ) 。
而通过理论知识我们知道 ( 还有上面的实验 )。 最终这个链是 __止__ 于 __null__ 的。


__如何能使这个链条更长呢? 使某一个__

看到上面的 Object 类型, 所有 非 function Object 的 function 类型的 原型指针 都指向了 function Object 的原型对象。

**也就是 __function__ 类型的, 可以将 原型链 连起来～**

那么怎么连起来呢？

假设，我们有 function A(){} ,  function B(){}

我想到了 2 种方法:

- 直接使 它们两个的 prototype 相等，即：

```
  A.prototype = B.prototype;
```

根据 JS 的引用赋值, 我们知道，A.prototype 与 B.prototype 现在指向的是同一个地址。

当任何一方改动这个 地址的值，另一方取得的值也改变了。

而且 指向 __构造函数 A__ 的指针( construct ) 指向了 __B__, ( 实例找不到爸爸了！ ), 而且！ 我们还不能改！

- 使 A.prototype 的 __原型__ 指向 B.prototype，也就是 `A.prototype.__proto__` --> B.prototype

我们知道, 一个 __构造函数__ 的 __实例__ 的 __原型__指针 (`__proto__`) 指向 __构造函数__的 __原型对象__( prototype )。

```
A.prototype = new B();
```
这样相当于， `A.prototype.__proto__` --> B.prototype,

当我们改变 A.prototype 的值的时候 B.prototype 并不会受影响

但是，由于 A.prototype 被覆盖( __重写__ ), __A.prototype.construct__ 也没有了, 但是！ 我们可以__手动添加__！ 并__不会__影响到 B.prototype！

```
A.prototype.construct = A;
```

综上，第二种方法，明显优于第一种。
而且第二种方法还有什么好处呢？

__我们不仅可以取到 B.prototype 里的各种方法, 属性。 而且，由于 A.prototype 是 B 的实例，A.prototype 的自身已经有了在 B 中声明的方法和属性。 这也就是我们所说的 继承。 __


---
写完这个, 想通了好多以前不理解的，开心Y(^_^)Y_)

写博客真好～

一定要坚持下去！！

{% blockquote %}

参考：
_JavaScript 高级程序设计_

{% endblockquote %}

