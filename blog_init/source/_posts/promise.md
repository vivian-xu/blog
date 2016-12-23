---
title: promise
categories:
- 编程
tags:
- promise
- ES6
- 抄抄抄
auto_spacing: true
---


### 定义

所谓 Promise, 简单说就是一个容器。里面保存着某个 __未来__才会 _结束的事件_ ( 通常是一个异步操作 ) 的 __结果__。

- Promise 对象是一个 __返回值的代理__，这个返回值在 promise 对象创建时未必已知。

- 它允许你为异步操作的成功返回值 ／ 失败信息指定处理方法。

- 这使异步方法可以像同步方法那样返回值： 异步方法会返回一个包含原返回值的 promise 对象来替代原返回值

<!-- more -->
Promise 对象有以下几种状态：

- pending
- fufilled 成功的操作
- rejected 失败的操作

pending 可转换带着 __一个成功值的 fulfilled 状态__,
也可变成一个 __带着失败信息的 rejected 状态__

- 当状态发生转换的时候， promise.then 绑定的方法就会被调用 

- 当绑定方法时， promise 对象已经处于 fulfilled / rejected 状态，则相应的方法会被 __立刻调用__ 

- 一个 promise 对象处在 fulfilled ／ rejected 状态时它也可以被称为 settled  状态。 ( resolved 表示 promise 对象处于 settled  状态，或 promise 对象被 __锁定__在调用链中 ) 

- 因为 Promise.prototype.then 和 Promise.prototype.catch 方法 __返回 promises  对象__，所以它们可以被链式调用





### 语法

new Promise( executor );
new Promise(function(resolve, reject) {...} );

参数：
executor

- 函数
- 默认有 resolve, reject 两个参数
- 在创建 Promise 对象的时候会 __立即执行__ ( 在 Promise 构造函数返回 Promise 对象之前就会被执行 )
- 调用 resolve 和 reject 函数会分别触发  promise 的成功或者 失败。
- 这个函数参数 通常被用来执行一些异步操作，操作完成后可以选择 调用 resolve 来触发 promise 的成功状态，或者，在出现错误的时候调用 reject 函数来触发 promise 的失败。
- 
### 属性

- Promise.prototype 表示 Promise 构造器的原型
- Promise.length 长度：1 ( 构造器参数数目 )

### 方法

- Promise.all( iterable )

  + 当 iterable 里的所有 promise 对象都成功的时候才会触发成功 
  
  +  任何一个 iterable 里的 promise 失败都会立刻触发该 promise 对象的失败 
  
  + 触发成功的状态后，会把一个 包含 iterable 里 __所有__ promise __返回值__ 的 __数组__作为成功回调的返回值 (顺序跟 iterable 的__顺序保持一致__)。

  + 触发失败的状态后, 会 __返回__ iterable 中__第一个触发失败的 promise 对象__ 的 __错误信息__( 作为它的错误信息 ) 

- Promise.race( iterable )
当 iterable 参数里的任意一个 promise 被成功或失败后，
父 promise 也会马上用子 promise 的成功返回值 或失败详情作为参数调用 父 promise 绑定的相应句柄, 并返回该 promise 对象。

NOTE !!! ： __这个是 promises ---> Promise.all  实现的原理__

- Promise.reject( reason )

返回一个 因为 reason 而被拒绝 (rejected) 的 Promise 对象

- Promise.resolve( value )

  + Returns a _Promise_ object that is resolved with the given value .  ( fulfill )

  + 如果该 value 为 thenable ( 带有 then 方法 )
      
      - 返回的 Promise 对象会 "跟随" 这个value 并采用这个 value 的最终状态 
 
  +  value 没有带 then 方法,  返回的 Promise will be fulfilled with the value
  
  +  如果你不知道 value 是不是一个 promise, Promise.resolve( value )  it instead and work with the return value as a promise. 

👆没有理解!! 看例子。。
--

### Promise prototype
#### 属性

- Promise.prototype.constructor

返回 the function that created an instance's prototype.
这个函数默认为是 _Promise function_ 

#### 方法

- Promise.prototype.catch( onRejected )

    + 向当前promise 添加 处理 rejection 的一个回调函数 onRejected， 

    + 如果这个回调函数被调用，将返回一个_ 新的 promise_
    
    + 这个 _新的 promise_ 会 resolve ( 解析 ) 这个回调函数的 value.
    
    + 如果 这个 _新的promise_ 的状态为 fulfilled, 就会被 resolve 它最原始的 fulfilled 状态
    
-  Promise.prototype.then(onFulfilles, onRejected )
    
    + 向当前的 promise 添加处理 fulfillment and rejection 的函数 onFulfiles, onRejected
    
    + 并且 ( 这个 then 函数 ) 返回一个_新的 promise _
    
    + 这个_新的 promise_ 解析 (resolving ) 处理函数 ( onFufilled / onRejected ) 的 返回值
    
    + 或者 如果这个 promise 没有被处理 , _这个新的 promise_ 解析成 它原来的 settled 值。 ( 比如说，相关的处理程序 onFulfilled 或者 onRejected 不是一个函数 )

```
let promiseCount = 0;

// 执行三次函数 ( 三个 promise )
testPromise();
testPromise();
testPromise();


function testPromise() {
  // 将全局的 count + 1 并赋值给自己的 小count
  let thisPromiseCount = ++promiseCount;

  let log = document.getElementById('log');
  
  log.insertAdjacentHTML('beforeend', `( ${thisPromiseCount} ) Start (<small> 同步数据开始啦~~ </small>)<br/>` );
  
  // 声明一个 promise 对象
  let p1 = new Promise(
    // 立刻开始执行
    function(resolve, reject) {
      
      log.insertAdjacentHTML('beforeend', `( ${thisPromiseCount} ) Started (<small> promise 异步数据 开始啦！(-promise-) </small>)<br/>`);
      
      // 设置 *** 后 resolve, 传入 小count 作为参数
      window.setTimeout(
        function() {
          resolve(thisPromiseCount);
          // reject(thisPromiseCount);
        }, Math.random()*2000 + 1000);
    
    }
  );
  

  p1.then(
    
    function(val) {
      log.insertAdjacentHTML('beforeend', `( ${val} ) made (<small> promise 异步数据 快完了 (-then-) </small>)<br/>`);
    }

  )
  .catch(
    
    function(reason) {
      log.insertAdjacentHTML( 'beforeend' ,`我被 拒绝 了！！！ ಥ_ಥ  因为 ${reason} `);
    }
 
  );
  
  log.insertAdjacentHTML('beforeend', `( ${thisPromiseCount} ) Promise fulfilled!  (<small> 同步数据 最后～  </small>)<br/>`);
}
```

结果：
当 resolve 的时候:

![](http://img.jackon.me/promise-resolve.png)

当 reject 的时候: 
![](http://img.jackon.me/promise-reject.png)

-- 

### 补充:

element.insertAdjacentHTML

##### 概念：
element.insertAdjacentHTML()
  - 将指定的文本解析为 _HTML 或 XML_
  - 并将生成的节点 __插入__到指定位置的 __DOM__ 树中。
  - 它不会__重新__解析它正在使用的元素, 因此它不会破环元素内的现有元素。这避免了 __额外__的序列化步骤，使其比直接 innerHTML __操作更快__

##### 语法

element.insertAdjacentHTML( position, text );

- position 是想对于 元素 ( element ) 的 __位置__, 并且必须是以下 _字符串_之一:

  - 'beforebegin' 元素自身的前面
  - 'afterbegin' 插入元素内部的第一个子节点之前
  - 'beforeend' 插入元素内部的最后一个子节点之后
  - 'afterend' 元素自身的后面

- text 
要被解析成 HTML / XML, 并插入到 DOM 树中的字符

位置名称的可视化：
--
```
<!-- beforebegin --> 
<p> 
<!-- afterbegin --> 
foo 
<!-- beforeend --> 
</p> 
<!-- afterend -->
```
<br/>
<br/>


{% blockquote %}
参考：
[MDN: Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)

[MDN: element.insertAdjacentHTML](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/insertAdjacentHTML)

{% endblockquote %}

还未看:
[We have a problem with promises](https://pouchdb.com/2015/05/18/we-have-a-problem-with-promises.html)

