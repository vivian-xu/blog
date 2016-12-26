---
title: 面试
categories:
- 编程
tags:
- 总结
- JS 基础
---


一面的三个题

虐，没有实际操作的结果就是概念不清

<!-- more -->

1. XMLHttpRequest && Promise

做了 2/3 吧
```
const inputDiv = document.querySelector("#input");
const rest = document.querySelector("#rest");

function showText(data) {
  rest.innerHTML = data;
//  rest.insertAdjacentHTML("beforeend",data);
}

function getTips(url, str){
  console.log('get')
  let count = 0;
  let p1 = new Promise((resolve,rejext)=>{
    count += 1;
    let xhr = new XMLHttpRequest();

    xhr.onload = ()=>{
      if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
        resolve(xhr.responseText.slice(0,20));
      }else{
        console.log(`something wrong ${xhr.status}`)
        reject(Error(`fufilled reason: ${xhr.status}`))
      }
    }

//   url += (url.indexOf('?') == -1) ? '?' : '&';
//   url += encodeURIComponent('value') + '=' + encodeURIComponent(str);

    xhr.open("get", url, true);
    xhr.send(str);
  });

  p1
    .then((val)=>{
    showText(val);
  })
    .catch((reason)=>{
     console.log( `I was fulfilled ,reason: ${reason}` )
  })
}

inputDiv.onkeyup = function(e){

  getTips( 'https://api.github.com/repos/facebook/react/forks',this.value );
  console.log(this.value);
}

```

[demo](http://jsbin.com/dilukoy/8/edit?js,console,output)

2. 判断类型
```
let arrayTest = [
  1,
  'str',
  true,
  null,
  undefined,
  function sayHi(){},
  [1,3,4],
  {
    name: '',
    age: ''
  }
]

let testResult = arrayTest.map((item, idx, array)=>{
  return Object.prototype.toString.call(item).slice(8,-1);
})

console.log( testResult );

// ["Number", "String", "Boolean", "Null", "Undefined", "Function", "Array", "Object"]
```

[jsbin demo](http://jsbin.com/dilukoy/3/edit?js,console)

3. 数组，重复的项,  排序

```
let arrayOne = [1,4,5]
let arrayTwo = arrayOne;
let arrayThree = arrayTwo;
let numa = 1;
let numb = 1;
let arrayTest = [
  1,
  'str',
  true,
  null,
  undefined,
  function sayHi(){},
  [1,3,4],
  {
    name: '',
    age: ''
  },
  numa,
  numb,
  arrayOne,
  arrayTwo,
  arrayThree

]
/*
[
  {
    item: xxx,
    count: 1
  }
]

*/


let result = arrayTest.reduce((pre, cur, idx, array)=>{
  let rightCount = -1;
  pre.some((ele, index)=>{
    rightCount = ele.item === cur ? index : -1;
    return ele.item === cur;
  })
  console.log( rightCount )

  if(rightCount !== -1){
    pre[rightCount].count += 1;
  } else {
    pre.push({
      item: cur,
      count: 1
    })
  }
  return pre;
}, [])

console.log(result);

result.sort((a, b)=>{
  return b.count - a.count
})

console.log(result);
```

[demo](http://jsbin.com/dilukoy/2/edit?js,console)


二面:

1. 做一个绕着大圆圆心旋转的小圆，小圆中的文字方向不会变化。

参考：[CSS 秘密花园 W3plus](http://www.w3cplus.com/css3/css-secrets/animation-along-a-circular-path.html)

第一种实现方法 ( 利用两个元素 ) ：
在字体元素上使用 相反的 animation ( animation-direction: reverse )
[demo](https://jsfiddle.net/vivian_xu/r91j1wc8/#&togetherjs=Rk7DRlE27r)

第二种实现方法 ( 利用 1 个元素):
实现原理是:
{% blockquote %}
transform-origin就是一个语法糖。你可以使用translate()替代。—— @Aryeh Gregor
{% endblockquote %}

这个意思大概是这样的:
```
   transform: rotate( 30deg); 
   transform-origin: 50% 150px; 
```
上面这个跟下面这个相等
```
  transform: translateX( 50%) translateY(150px) rotate(30deg) translateX(-50%) translateY(-150px);
  transform-origin: 0 0;
```
相当于, 先将 元素 平移到 圆点位置，然后在做需要的变换，最后在 反向平移回去。

而最重要的一点在于：transform 属性不仅应用在元素上，而且应用到了元素所在的整个 __坐标系统__。这也说明为什么不同的transfrom顺序很重要，不同顺序的相同转换可能前生的结果会完全不同。

首先，__不运用 animation-direction__ 的 __2__ 个 元素合作的解决方法
[demo](https://jsfiddle.net/vivian_xu/r91j1wc8/#&togetherjs=Rk7DRlE27r)

因为是同样的 __transform-origin__ ,  我们可以将 __两个元素的 transform 放！一！块！__
[demo](https://jsfiddle.net/vivian_xu/r91j1wc8/1/#&togetherjs=Rk7DRlE27r)

由于是一直转圈的，所以可以将相邻的， translateX 和 translateY 合并，抵消。
[demo](https://jsfiddle.net/vivian_xu/r91j1wc8/2/#&togetherjs=Rk7DRlE27r)

可以把要旋转的元素，直接放到中心点去( margin )，这样就不用先将它移到中心点，因此可以少两个 translateY()
[demo](https://jsfiddle.net/vivian_xu/r91j1wc8/2/#&togetherjs=Rk7DRlE27r)

最终的 @keyframes
```
@keyframes circle {
  from {
    transform: rotate(0turn) translateY( -150px )
    translateY( 50% ) rotate(1turn);
  }  
  to {
    transform: rotate(1turn) translateY(-150px )
    translateY( 50% ) rotate(0turn) ;
  }
}
```

其中: transform 最后一部分 即 (  from 中的 rotate(1turn) , to 中的 rotate(0turn) 是对 文字部分的调整 )


2. 使用 css 选择最后一行的元素
如图：

![](http://img.jackon.me/flex.png)

主要是利用 伪类的连用

[demo](https://jsfiddle.net/vivian_xu/r91j1wc8/3/#&togetherjs=Rk7DRlE27r)

3. JS 执行进程 && promise
在网上查找相关信息，发现 _JavaScript 高级程序设计_ 里有讲一点这一部分，在 __高级技巧__ 中的 _高级定时器_ !

简单来说，是 JS 是运行在单线程的环境中，它的进程会阻塞其他的页面处理，所以必须有小间隔来防止用户界面被锁定。( 代码长时间运行中可能出现 )

