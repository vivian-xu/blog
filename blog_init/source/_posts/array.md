---
title: 数组
categories:
- 编程
tags:
- 抄抄抄
- 数组
- JS 基础
---
### 特征:

  - 有序列表
  - __每一项__ 都可以保存 __任意类型__ 的值
  - 大小可以动态调整

### 创建方式

  - 构造函数 ( __也可以省略掉 new __ )

```
var colors = new Array(); 

// 如果预先知道元素数量
var colors = new Array(20);

// 如果预先知道要包含的项
var colors = new Array("red", "blue", "green" );

// 如上两个所示，给构造函数传入 number 类型参数 --> length 为 number 的数组
// 给构造函数传入 其他类型参数 --> 创建包含那个值的只有一项的数组
```
<!-- more -->

  - 数组字面量

数组字面量由一对包含数组项的方括号表示，多个数组项以逗号隔开

```
var colors = ["red", "blue", "green"];

var values = [1,2,]; //  会创建一个包含 2  或 3 项的数组
```

### 访问

- 使用 __方括号__, 并提供 相应值的基于 0 的数字的索引

```
var colors = ["red", "blue", "green"];
alert( colors[0] ); // 显示第一项
```

### 设置

设置数组的值与访问语法相同，但会替换指定位置的值。

如果设置某个值的索引超过数组现有的项数，则会自动增加到该 __索引值 + 1_  的长度

### length

- 数组的项数保存在其 __length__ 的属性中
- length > = 0
- 可以通过改变 length 的值，向数组添加 ( 新增的每一项都为 undefined ) 或者减少 ( 减少的那几项是 undefined ) 项

```
var colors = ["red", "blue", "green"];    
colors[99] = "black";                            // 在位置 99 添加一种颜色

alert( colors.length );    //100
alert( colors[0] ); // 显示第一项
```
上例 3 到 98 实际上是 __不存在的__ , 所以访问它们都将返回 __undefined__

### 检测数组 Array.isArray()

### 转化方法 

- __所有的对象__ 都具有 __toLocaleString()__, __toString()__, __valueOf()__ 方法

  + toString() 返回由数组中每个值的字符串形式拼接而成的一个 __以逗号分隔的字符串__。 它会为取得每一项，调用每一项的 __toString()__ 。

  + __valueOf()__ 返回 __数组__

  + __toLocaleString()__  它也会创建一个数组值的以逗号分隔的__字符串__。 不同的是， 它会为取得每一项，调用每一项的 __toLocaleString()__ 。 而不是 __toString()__

NOTE: alert() 会在后台调用 toString() 方法

- 使用 join()
  
  + 可以使用不同的分隔符来构建这个字符串。
  
  + 一个参数，作为分隔符的 __字符串__
  
  + 如果不给 join() 传参数 ／ 传入 undefined 则使用 "__,__" 作为分隔符。 

```
var colors = ["red", "blue", "green"];    
console.log( colors.join("-") ); // red-blue-green
```

###方法

#### 栈方法

栈是一种 __后进先出__ 的数据结构。
在栈中项的插入 ( 叫做__推入 push__ ), 移除( 叫做 __弹出 pop__ ), 只发生在一个位置 --- __栈的顶部__

ECMAScript 为数组专门提供了 __push()__ 和 __pop()__ 方法，以便实现类似 栈 的行为。

- push()

  + 接收任意数量的参数，
  + 将它们 逐个添加到 数组末尾，
  + _返回修改后数组的__长度__。_

- pop()

  + 从数组的末尾 __移除最后一项__，
  + 减少数组的 __length__ 值，
  + 然后返回 __移除的项__

```

const colors = new Array();
const count = colors.push("red", "green");

console.log(count); // 2
console.log(colors ); // ["red", "green"]

var item = colors.pop();
console.log( item ); // "green"
console.log(colors); // ["red"]
```
#### 队列方法

队列数据结构的访问规则是 __先进先出__

队列在列表的 __末端添加项__，
从列表的__前端移除项__

- push()  与上面相同

- shift() 
 
  + 从数组的前端 __取得项__
  + 移除数组的第一个项
  + 返回移除的那个项
  + 同时长度 - 1

```
const colors = new Array();
const count = colors.push("red", "green");

console.log(count);  // 2
console.log(colors ); // ["red", "green"]

colors.push("black");  // 3

var item = colors.shift();
console.log( item );  // red
console.log(colors);   ["green", "black"]
console.log(colors.length); // 2
```

- unshift()

  + unshift() 与 shift() 用途相反，
  + 在数组的前端添加任意个项
  + 返回新的数组的 __长度__


unshift() 和 pop() 可以实现相反方向的队列
- 在数组前端添加项
- 在数组末端删除项

```
const colors = new Array();
const count = colors.unshift("red", "green");

console.log(colors); // ["red", "green"]
console.log(count); // 2

colors.unshift("black");

console.log(colors); // ["black", "red", "green"]

var item = colors.pop();

console.log( item ); // "green"
console.log(colors); // ["black", "red"]
console.log(colors.length);
```
unshift() 和 shift() 可以实现相反方向的栈

#### 重排序方法

- sort()
  
  + 默认情况下， sort()  按__升序__排列数组
  + 会 先调用每个数组项的 __toString()__ 转型方法 --> 得到字符串
  + !!!!!  然后 比较__得到的字符串__
  

  + sort() 方法可以接收一个 __比较函数__ 作为参数，以便指定 哪个值 位于 哪个值 的前面。
  
  + 比较函数：

    - 接收 __两个参数__
    - 返回一个负数 ------- 第一个参数 在 第二个参数之前
    - 返回一个正数 ------- 第一个参数 在 第二个参数之后

如下：

只要将比较函数作为参数传给 sort() 方法即可。

NOTE:  __reverse() 不可以将 比较函数当 参数__
```
function compare(v1, v2) {
  if( v1 < v2 ) {
    return -1;
  } else if( v1 > v2 ) {
    return 1;
  } else {
    return 0;
  }
}

const arrb = [1,23,4,6,76,1,1,535,2];

console.log( arrb.sort(compare) ); // [1, 1, 1, 2, 4, 6, 23, 76, 535]
```
上面的 compare() 函数适用于大多数数据类型，

对于__数值类型__ 或者其 __valueOf()__ 返回 __数值类型 __的对象类型，可以使用一个比较简单的函数

```
function compare(v1, v2 ) {
  return v1 - v2;
}
```


- reverse()   反转数据项的顺序

```
const arrb = [1,23,4,6,76,1,1,535,2];
console.log( arrb.reverse() ); // [2, 535, 1, 1, 76, 6, 4, 23, 1]
```

#### 操作方法

- concat()  ------ 不改变原数组

  + 创建当前数组的一个 __副本__
  + 将接收到的参数 __添加__到这个副本的末尾
  + 返回 __新构建的数组__
  + __不会影响 原数组__

  + 如果传递的不是数组，这些值就会被简单的添加到结果数组的末尾
  + 如果没有传递参数，它只是 __复制__ 当前的数组并返回副本
  
```
const arrb = [1,23,4,6,76,1,1,535,2];

let arrc =  arrb.concat('a', 'b');

console.log(arrc);  // [1, 23, 4, 6, 76, 1, 1, 535, 2, "a", "b"]

let arrd = arrb.concat('y', ['s', '2']); 

console.log(arrd); // [1, 23, 4, 6, 76, 1, 1, 535, 2, "y", "s", "2"]

console.log(arrb); // [1, 23, 4, 6, 76, 1, 1, 535, 2]

- slice()  ------ 不改变原数组

  + 能够基于 当前数组中的一个或者多个项 __创建一个新数组__，

  + 参数： 接受一个或者两个参数 (  返回项的 __起始位置__和__结束位置__ )
    
    - 一个参数： 返回 从该参数指定位置到当前数组末尾的所有项
    
    - 两个参数： 返回起始位置到结束位置(  _不包括结束位置_) 的所有项

    - 如果参数有 __负值__，则用 _数组长度加上该数_来确定相应的位置。

```
const arrb = [1,23,4,11,'a', 'b', 'v', 535,2];

let arrc =  arrb.slice(5);

console.log(arrc);  // ["b", "v", 535, 2]

let arrd = arrb.slice(0,6);

console.log(arrd); // [1, 23, 4, 11, "a", "b"]

let arre = arrb.slice(-5,6);

console.log(arrb.length); // 9

console.log(arre); // ["a", "b"]

```

#### splice() 

有很多种用法

- 主要方式： 向数组的中部插入项

使用主要这种方式的有 3 种：

- 删除

可以删除任意数量的项，需指定 2 个参数： 要删除第一项的位置 和 要删除的项数。

- 插入

可以向 __指定位置__ 插入任意数量的项。需指定 __至少 3 个参数__： 起始位置， 0 ( 要删除的项数 )，要插入的项 ( 可多个 )

- 替换

可以向指定位置插入任意数量的项，且同时删除任意数量的项。 需指定 3 个参数：起始位置，要删除的项的数目，要插入的任意数量的项 ( _插入的项数不必与删除的项数相等_)

- 返回

splice()  始终都返回一个数组，该数组包含着从原始数组中删除的项

```
const arrb = [1,23,4,11,'a', 'b', 'v', 535,2];

// 删除
let arrc =  arrb.splice(0,1);
console.log(arrc); //  [1]
console.log(arrb); // [ 23,4,11,'a', 'b', 'v', 535,2 ]

// 插入
let arrd = arrb.splice(1, 0, 'hello','hi');

console.log(arrd); // [ 23, 'hello', 'hi',4,11,'a', 'b', 'v', 535,2 ]
console.log(arrb); // []

// 替换
let arre = arrb.splice(3, 2, 'one', 'two');
console.log(arrb); // [ 23, 'hello', 'hi', 'one', 'two','a', 'b', 'v', 535,2 ]
console.log(arre); // [4, 11]

```
#### 位置方法

- indexOf()
  从数组的开头开始向后查找

- lastIndexOf()
  从数组的末尾向前查找

- 这两个参数都接收 2 个参数： 要查找的项， 查找起点位置的索引 ( 可选 )
 
- 返回 要查找的项在数组的位置，或者没找到返回 -1
- 在比较第一个参数和数组中的每一项时，__使用全等__

```
const arrb = [10  ,23,4,11,666, 'b', 'v', a1,2];

console.log(arrb.indexOf(666) ); // 7
```
#### 迭代方法 ----- 不修改原数组

- every()  对数组中的 __每一项__运行指定函数，如果该函数 __每一项都返回 true__ ,  则返回 _true_, __ 当一项不满足则返回 false， 不再运行下面的项__

返回 true / false

- filter() 对数组中的 __每一项__运行指定函数，返回该函数会返回 _true_ 的项组成的数组

返回 数组

- forEach() 对数组中的 __每一项__运行指定函数 __无返回值__( 只能返回 undefined )

返回 undefined ( 无返回值 )

- map() 对数组中的 __每一项__运行指定函数，返回每次函数调用的__结果组成的数组__

返回 数组

- some() 对数组中的 __每一项__运行指定函数，如果该函数中的__任一项__会返回 true, 则返回 true

返回 true / false

```
let numbers = [1,5,67,8,3,1,7];

let everyResult = numbers.every((item, idx, array) => {
  console.log(item);
  return item > 6;
})

console.log(everyResult ); // false

let filterResult = numbers.filter((item, idx, array)=>{
  let rest = array.slice(0, array.indexOf(item)).concat(array.slice(array.indexOf(item)+1,array.length ));
  return rest.indexOf(item) >= 0 ? true: false  ;
})

console.log(filterResult); // [1, 1]

let forEachResult = [];
numbers.forEach((item, idx, array) => {
  forEachResult[idx] = item -1 ;
})

console.log(forEachResult); // [0, 4, 66, 7, 2, 0, 6]

let mapResult = numbers.map((item,idx,array)=> {
  return item-1;
})
console.log(mapResult); // [0, 4, 66, 7, 2, 0, 6]

let someResult = numbers.some((item, idx, array)=>{
  return item > array[idx-1];
})
console.log(someResult); // true
```

#### 归并方法

- reduce()
- reduceRight()

- 迭代所有项
- 构建一个最终返回的值

参数
- 每一项上调用的 函数 
  
  + 这个函数接收 4 个参数: 前一个值， 当前值， 项的索引， 数组对象
  
  + 这个函数 __返回的任何值__都会作为 __第一个参数__自动传给 __下一项__

- 初始值 ( 可选 )

```
let numbers = [1,5,67,8,3,1,7];

let reduceResult = numbers.reduce((pre, cur, idx, array) => {
  let item = idx > 0 ? cur + pre[idx-1] : cur;  // 每一项和已经得到的之前的一项相加
  pre.push(item);
  return pre;
}, []);

console.log( reduceResult );  // [1, 6, 73, 81, 84, 85, 92]
```
{% blockquote %}
参考：

_JavaScript 高级程序设计_
{% endblockquote %}


