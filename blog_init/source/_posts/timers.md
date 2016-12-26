---
title: JS 定时器
categories:
- 编程
tags:
- 抄抄抄
- JS 基础
- 定时器
---

假设没有 Web Works

### 基本知识

JavaScript 的运行是 __单线程__的，而定时器只是 __计划__ 代码在未来某个时间执行。 而执行的时机是__不能__保证的。

因为在页面的使命周期中，不同的时间可能有其他的代码在控制 ( 我的理解是 _占用_ ) JavaScript 进程 。

在页面下载完成后的代码运行，事件处理程序，Ajax __回调函数__都必须在同一个线程中执行。

__实际上，浏览器负责进行排序，指派某段代码在某个时间点上运行的优先级。__

可以把 JS 想象成在时间线 ( __一维__ ) 上运行的。
当页面载入时, 首先执行的是任何包含在 <script> 元素中的代码, 通常是页面生命周期后面要用到的一些简单的函数和变量的声明, 不过有时候也包含一些初始数据的处理。 在这之后， JS 进程将等待更多代码执行。__当进程空闲的时候，下一个代码会被触发并立刻执行。__ 例如，当点击某个按钮时，当 JS 进程处于空闲状态， onclick 事件处理程序会立刻执行。

除了主 JS 执行进程外，还有一个需要在进程下一次空闲时执行的 __代码队列__。
例如，当某个按钮被按下时，它的事件处理程序代码就会被添加到 __队列__ 中，并在下一个可能的时间里执行。当接收到某个 __Ajax__ 响应时，__回调函数__ 的代码会被添加到__队列__。

**在 JS 中没有任何代码是立刻执行的，但一旦进程空闲则尽快执行** 。

![](http://img.jackon.me/js.svg)

虽然 JS 的引擎是单线程的，但是 JS 的运行环境往往不是单线程的。
比如，浏览器。浏览器有 Javascript 引擎线程 ( 用于处理 JS )，浏览器事件触发线程 ( 用于控制交互 )，Http 请求线程，GUI 界面渲染线程 ( 用于页面渲染 ) 等。

- JS 引擎是基于事件驱动的，采用的是单线程运行机制。即 JS 引擎只会顺序的从任务列表中取任务执行。
- 浏览器事件触发线程：当一个事件被触发的时候，该线程会把事件添加到待处理队列的队尾，等待 JS 引擎处理。
- GUI 渲染线程：负责渲染浏览器界面，当界面需要重绘( Repaint ) 或由于某种操作引发回流 ( Reflow ) 时，该线程就会执行。
NOTE: GUI 渲染线程与 JS 引擎是 __互斥__的。因为 JS 可以操作 DOM 元素，会影响 GUI 的渲染结果。所以当 __JS  引擎__执行时 __ GUI 线程__就会被 __挂起__， __GUI 更新__会被保存在一个__队列__中等到 JS 引擎空闲时立即被执行。

#### 定时器 setTimeout()

- 定时器的工作方式是，__当特定的时间过去后，将代码插入到队列( 并不意味着立刻执行 )。__

- 关于定时器 __最重要的事情__是, __指定的时间间隔表示何时将定时器的代码添加到队列，而不是何时实际执行代码。__

- __由于 JS 进程会阻塞其他页面处理，所以必须有这些小间隔来防止用户界面被锁定( 代码长时间运行中可能出现 )。__

设置一个定时器，确保在定时器代码执行前至少有一个 __进程间隔__

#### 重复的定时器 setInterval()

使用 setInterval() 创建的定时器确保了定时器代码规则地插入队列。

但是，这样定时器代码再次被被添加到队列之前还没有完成执行，结果导致定时器代码连续运行好几次。

所以，__ 当使用 setInteval() 时，仅当没有该定时器的任何代码实例时，才将定时器代码添加到队列中。__

这样确保了定时器代码加入到队列中的__最小时间间隔__为__指定间隔__。

但是这种重复定时器的规则有两个问题：第 1 个问题，某些间隔会被跳过。第 2 个问题，多个定时器的代码执行之间的间隔会比预期的小。

比如：某个 onclick 事件处理程序使用 setInterval() 设置了一个 200ms 间隔的重复定时器。如果事件处理程序花了 300ms 完成，同时定时器代码也花了差不多的时间，就会同时出现跳过间隔且连续运行定时器的代码的情况。

_为了避免 setInterval() 的重复定时器的这 2 个缺点，你可以通过用如下模式使用链式 setTimeout() 调用。_

```
setTimeout(()=>{
  // do somethings

  setTimeout(arguments.callee, interval);
}, interval);
```

这个模式链式调用了  setTimeout(), 每次函数执行的时候都会创建一个新的定时器。第二个 setTimeout() 调用使用了 arguments.callee 来获取对当前执行的函数的引用，并为其设置另外一个定时器。

这样做的好处是：

- 在前一个定时器代码执行完成之前，不会向队列插入新的定时器代码，确保不会有任何缺失的间隔。

- 它可以保证在下一次定时器代码执行之前，__至少__要等待指定的间隔，__避免了连续的运行。__

这个模式主要用于重复定时器，如下：

```
setTimeout(()=>{
  let div = document.getElementById('myDiv');
  left = parseInt(div.style.left) + 5;
  div.style.left = left + "px";

  if( left < 200 ) {
    setTimeout(arguments.callee, 50);
  }
}, 50 );
```
这段定时器代码每次执行的时候将一个 <div> 元素向右移动，当左坐标在 200 像素的时候停止。

NOTE：
每个浏览器窗口，标签页，或者  frame 都有其各自的代码执行队列。这意味着，进行跨  frame 或者 跨窗口的定时调用，当代码同时执行的时候可能会导致竞争条件。无论何时需要使用这种通信类型，最好是在接收懂  frame 或者 窗口中创建一个定时器来执行代码。

理解这个图就 ok 了！
![](http://img.jackon.me/Timers.png)

###  Yielding Processes

运行在浏览器中的 JavaScript 都会被分配一个确定数量的资源。并且 浏览器严格限制了 JavaScript 需要的内存大小和处理时间。
其中一个限制是长时间运行脚本的限制，如果代码运行超过了特定的时间或者特定的语句数量，就会弹出一个浏览器错误的对话框，告诉用户，某个脚本会用过长的时间执行，询问是否允许继续执行。
所有 JavaScript 开发人员的目标就是确保用户永远不会在浏览器中看到这个对话框。

定时器是绕开此限制的方法之一。

脚本长时间运行的问题通常是由两个原因之一造成的： 过长的，过深嵌套的函数调用，或者是 进行大量处理的循环。
这两者中，后者是比较容易解决的。
长时间的循环通畅遵循以下模式：

```
for (var i=0, len = data.length; i<len; i++ ){
  process( data[i] );
}
```

这个模式问题在于  __要处理的项目的数量__ 在运行前是 __不可知的__。 数组中的的项目数量之间关系到该数组循环的时间长度。同时由于 JS 的执行是一个 __阻塞操作__，脚本运行花费的时间越久，用户__无法__与页面交互的时间越久。

 在展开该循环前，你需要回答这两个问题：

- 该处理是否必须同步完成？如果这个数据的处理会造成其他运行的阻塞，那么最好不要改动它。不过，如果你的回答是__否__, 那么可以将某些处理推迟。

- 数据是否必须按顺序完成？如果项目的顺序不是非常 重要，可能可以将某些处理推迟。

当你发现某个循环占用了大量的时间，同时对于上述的两个问题，你的回答都是 __否__, 那么你就可以使用 __定时器分割这个循环__。
这是一种叫做 __数组分块__ 的技术。小块小块的处理数组。
基本思路就是为要处理的项目 __创建一个队列__, 然后使用 __定时器__ 取出下一个要处理的项目进行处理，接着在 __设置另一个定时器__。基本模式如下：

```
setTimeout( ()=>{

  var item = array.shift();
  process( item );

  if( array.length > 0 ) {
    setTimeout(arguments.callee, 100);
  }
} , 100);
```

在这个模式中， array 变量本质上就是一个 “待办事宜” 列表，它包含了要处理的项目。 使用 shift() 方法可以获取队列中的下一个要处理的项目，然后将其传递给某个函数。如果队列中还有项目，则设置另一个定时器，并通过 arguments.callee 调用同一个匿名函数。

要实现数组分块可以使用以下函数：

```
/**
 * @ array 要处理的项目的数组
 * @ process 处理项目的函数
 * @ context 运行该函数的执行环境
 */

function chunk(array, process, context) {
  setTimeout( ()=>{
    var item = array.shift();
    process.call( context, item);

    if( array.length > 0 ) {
      setTimeout( arguments.callee, 100 );
    }
  }, 100);
}
```
定时器的时间间隔设置了 100 ms , 使得在  JS 进程在处理项目的时间 间 转入空闲。

__ NOTE: __
chunk() 是会 __改变__ 传递进来的数组的。所以你想保持原数组不变，则应该将该数组的副本 传递给 chunk() , 如:

```
chunk( data.concat(), printValue );
```

数据分块的__重要性__在于：它可以将多个项目的处理在 __执行队列上分开__，在每个项目处理之后，__给予其他__的浏览器__处理机会__，_这样就可能避免长时间运行脚本的错误_。

NOTE:
一旦某函数需要花 __50ms__  以上的时间完成，那么最好看看是否能将任务分割。


#### 函数节流

浏览器中某些计算和处理要比其他的耗内存很多。例如： __DOM 操作__比起非 DOM 交互需要更多的内存和 CPU 时间。连续尝试进行过多的 DOM 操作，可能会导致浏览器挂起，甚至崩溃。

尤其在 IE 中使用 onresize 事件处理 ( 调整浏览器大小的时候，会连续触发 )，并在其内部尝试进行 DOM 操作，其高频的更改可能会让浏览器崩溃。

为了这个问题，可以使用定时器对该函数进行 __节流__。

函数节流的基本思想就是 ，__某些代码__不可以在没有间断的情况 __连续重复__执行。

第一次调用函数，创建一个定时器，在指定的时间间隔后运行代码。

第二次调用函数，__先清除前一次的定时器__，然后设置另一个。
如果前一次的定时器还没有执行，就相当于将其替换为一个新的定时器。

目的在于 只有在执行函数的请求 __停止了一段时间后__才__开始执行__。

以下是该模式的基本形式：

```
var processor = {
  timeoutId: null,

  performProcessing: function() {
    console.log(new Date())
  },

  process: function() {
    clearTimeout(this.timeoutId);

    this.timeoutId = setTimeout(()=>{
      console.log('hi');
      this.performProcessing();
    }, 100);
  }
};

processor.process();
```

在这段代码中，创建了一个 processor 对象。

这个对象有 2 个方法：

process( ) --- 初始化任何处理所必须调用的。

performProcessing( ) --- 实际进行的处理。

调用 process( ) 时, 先 清除存好的 timeoutId, 来阻止之前的调用被执行。
然后创建一个新的定时器调用 performProcessing( )。

时间间隔是__100__ ，代表 __最后一次__调用 process( ) 之后__至少__ 100ms 后才会调用 performProcessing( ) 。假如 100ms 内调用 process( ) 共 __20__ 次，performanceProcessing( ) 仍只会被调用 1 次。

这个模式可以使用 throttle( ) 函数来简化，这个函数可以自动进行定时器的设置和清除，如下：

```
function throttle( method, context ) {
  clearTimeout( method.tId );
  method.tId = setTimeout( () => {
    method.call( context );
  }, 100);
}
```

如果你使用 resize 事件来改变页面布局的话，最好控制处理的频率，以确保浏览器不会在极短的时间内进行过多的计算。

例如： 假设有 <div>  元素需要保持 高度 和 宽度 相同。

```
window.onresize = function() {
  let div = document.getElementById("myDiv");
  div.style.height = div.offsetWidth + "px";
}
```

上面有 两个问题 可能会导致浏览器运行缓慢。

第一， 要计算 offsetWidth 属性。如果该元素或者页面上其他元素有非常复杂的 CSS 样式，那么这个过程会很复杂。

第二，设置 __某个元素的高度__需要对页面进行 __回流__ 来令改动生效。

这样，就可以使用 throttle( ) 函数。

```
function resizeDiv( ) {
  let div = document.getElementById("myDiv");
  div.style.height = div.offsetWidth + "px";
}

window.onresize = function() {
  throttle( resizeDiv );
}
```

这样, onresize 事件处理程序调用 throttle() 并传入 resizeDiv 函数，而不是直接调用 resizeDiv()。

只要代码是 __周期性__执行的，都应该使用 __节流__。
但是不能控制请求执行的速率。

{% blockquote %}
参考：

_JavaScript 高级程序设计_

[How JavaScript Timers Work](http://ejohn.org/blog/how-javascript-timers-work/)

[js事件线程机制和异步执行](http://www.cnblogs.com/gyx19930120/p/4419875.html)

[知乎: javascript既然是单线程语言 ， 为什么会分主线程和消息线程(event loop) ?](https://www.zhihu.com/question/35905242?sort=created)

[谈谈 JavaScript 的异步实现](http://www.cnblogs.com/sprying/archive/2013/05/26/3100639.html)

[Javascript线程分析](http://www.coin163.com/it/2870178198278375178/javascript-)
{% endblockquote %}




