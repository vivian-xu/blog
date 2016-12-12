---
title: 闭包
categories:
- 编程
tags:
- 抄抄抄
- JS 基础
auto_spacing: true
---


### 基础知识

 - 闭包是指有权访问另一个函数作用域中的变量的函数。

 - 创建闭包的常见方式：在 一个函数内部创建另一个函数

```
function createComparisonFunction(propertyName) {
    return function(obj1, obj2) {
        var v1 = obj1[propertyName]; // 这里两行访问了外部函数的变量
        var v2 = obj2[propertyName]; // 这里两行访问了外部函数的变量

        if(v1 < v2) {
            return -1;
        } else if (v1 > v2) {
            return 1;
        } else {
            return 0;
        }
    };
}

```
<!-- more -->
如上面例子，内部函数访问了外部函数的变量，即使这个内部函数被返回了，在其他地方被调用了，依旧能访问外部变量 propertyName。

 - 为什么呢？

   简单的说是因为内部函数的**作用域链**中包含了外部函数 createComparisonFunction 的作用域。


#### 作用域链：

  - 本质上是一个指向变量对象的指针列表。

  - 当某个函数被调用的时候，会创建一个执行环境( execution context )及相应的作用域链。

  - 然后，使用 arguments 和其他命名参数的值来初始化函数的活动对象( activation object )。

  - 在作用域链中，外部函数的活动对象始终处于第二位，外部函数的外部函数的活动对象处于第三位。。。。直至作用域链终点的全局执行环境。
  - 无论在什么时候函数访问一个变量的时候，就会从作用域链中搜索具有相应名字的变量。


 __为了了解调用函数后都发生了什么我们先从一个简单的例子下手：__

```
function compare(v1, v2) {
    if(v1 < v2) {
        return -1;
    } else if (v1 > v2) {
        return 1;
    } else {
        return 0;
    }
}

var result = compare(5, 10);

```

当调用 compare() 时，会创建一个包含 arguments, v1, v2 的**活动对象**。 全局执行环境的变量对象（包含 compare() 和 result）在 compare() 执行环境的作用域链中处于第二位。


**每个执行环境都有一个表示变量的对象 -- 变量对象**。全局执行环境的变量对象始终存在，而像 compare（） 函数这样的局部环境的变量对象，只在函数**执行过程**中存在。

 - 在**创建** compare() 函数时，会创建一个**预先**包含**全局变量对象**的作用域链，这个作用域链被保存在 内部的 **[[Scope]]** 属性中。

 - 当调用 compare() 时，会为函数创建一个执行环境，然后通过**复制 [[Scope]]** 属性中的对象**构建**起执行环境的**作用域链**。

 - 然后，又有一个**活动对象**（函数的变量对象）被创建并被**推入**执行环境作用域的前端。

在上面的例子中，compare() 执行环境的作用域中包含两个变量对象：本地活动对象和全局变量对象。

无论在什么时候函数访问一个变量的时候，就会从作用域链中搜索具有相应名字的变量。

##### 当函数执行完后，局部的活动对象就会被销毁，内存中仅保存全局作用域（全局执行环境的变量对象）。  但是，闭包不同。


在一个函数内部定义的函数将会包含（外部函数）的活动对象添加道它的作用域链中。 因此在第一个例子中，如下：

```
function createComparisonFunction(propertyName) {
    return function(obj1, obj2) {
        var v1 = obj1[propertyName]; // 这里两行访问了外部函数的变量
        var v2 = obj2[propertyName]; // 这里两行访问了外部函数的变量

        if(v1 < v2) {
            return -1;
        } else if (v1 > v2) {
            return 1;
        } else {
            return 0;
        }
    };
}

```

在 createComparisonFunction() **内部定义**的**匿名函数**的作用域链中，实际上将会包含外部函数 createComparisonFunction() 的活动对象。

在匿名函数从createComparisonFunction() 中返回后，它的作用域链被初始化为 **包含 createComparisonFunction() 函数的活动对象和全局变量对象**。

这样匿名函数就可以访问在 在匿名函数从createComparisonFunction 中定义的变量了。

更重要的是 createComparisonFunction 函数在执行完后，其活动对象也不会被销毁，因为
匿名函数的作用域链仍然在引用这个活动对象。（也就是说，当createComparisonFunction 函数返回后，其执行环境的作用域链会被销毁，**==但他的活动对象留在了内存中==**，直到匿名函数被销毁。

例如：
```
// 创建函数
var compareName = createComparisonFunction("name");

// 调用函数
var result = compareName({name:"Nicholas"}, {name: "Greg"});

// 解除对匿名函数的引用 (以便释放内存)
compareName = null
```
**通过将 compareName = null 解除该函数的引用**，就等于通知垃圾回收例程将其清除。


### 闭包与变量

**作用域链这种机制引出一个值得注意的副作用： 闭包只能取得包含函数中任何变量的 **最后一个值**。 如下：

```
function createFunctions(){
    var result = new Array();

    for(var i =0; i<10; i++) {
        result[i] = function(){
            return i;
        };
    }

    return result;
}
```

这个函数会返回一个函数数组。

表面上看应该返回的数组类似于 [0,1,2,3 ....] 这个样子的，但是实际上返回的是 **[10, 10, 10, 10 ...]** 这个样子的。

因为**每个函数**的作用域链上都保存着createFunctions 函数的**活动对象**，所以他们都引用的是**同一个变量 i**。 当createFunctions 函数**返回**后， 变量 i 的值是 10。

但是， 我们可以通过创建另一个匿名函数强制让闭包的行为符合预期。如下：

```
function createFunctions(){
    var result = new Array();

    for(var i =0; i<10; i++) {
        result[i] = (function(num){
            return function(){
                return num;
            };
        })(i);
    }

    return result;
}
```

这样，每个函数就会返回各自不同的索引值了。

在这个版本中，我们没有直接把闭包赋给数组，而是定义了一个匿名函数，并将立即执行该函数的结果赋给数组。 这个匿名函数有一个参数 num ,也就是最终函数要返回的值。

在调用每个匿名函数时，我们传入了变量 i， **函数参数是按值传递的**，所以就会将变量 i 的当前值复制给 参数 num, 而在这个匿名函数的**内部**，又创建并返回了一个访问 num 的**闭包**。这样， result 数组中的**每个函数**都有自己 num 变量的一个**副本**，因此就可以返回各自不同的数值了。


### 闭包中的 this

- this 对象是在运行时基于函数的执行环境绑定的。
- 全局函数中， this === window, 当函数被当作某个对象的方法调用时候， this 等于那个对象。
- 匿名函数的执行环境具有全局性，因此其 this 对像，通常指向  window 。但由于编写闭包的方式不同，这一点可能不会那么明显。 如下：

```
var name = "The Window";

var object = {
    name: "My Object",

    getNameFunc: function(){
        return function(){
          return this.name;
        };
    }
};

alert(object.getNameFunc()()); // "The Window" 在非严格模式
```
- 调用 object.getNameFunc()() 会立即调用它返回的函数，结果就是返回一个字符串。 （ The Window ／ 全局 name 变量的值 )

- **为什么匿名函数没有取得其外部作用域的 this ?**

- 每个函数在被**调用**的时候都会**自动取得**两个特殊
变量： **arguments 和 this**。

- 内部函数在搜索这两个变量时，只会搜索到**其活动对象为止**，因此永远不可能直接访问到外部函数中的这两个变量。

- 不过，如果把外部作用域中的 this 对象保存在一个闭包能够访问到的变量中，就可以让闭包访问该对象了。

```
var name = "The Window";

var object = {
    name: "My Object",

    getNameFunc: function(){
        var that = this;

        return function(){
          return that.name;
        };
    }
};

alert(object.getNameFunc()()); // "My Object"
```

我们将  this 对象赋值给了一个名叫 that 的变量。所以在定义了闭包之后，闭包也可以访问这个变量。即使在函数返回了之后，that 也仍然引用着 object ，所以调用 object.getNameFunc()()就返回了 My Object.


- 在几种特殊的情况下，this 的值可能会意外地改变。 比如：

```
var name = "The Window";

var object = {
    name: "My Object",

    getName: function(){

        return this.name;

    }
};

```
这里的 getName() 方法只简单地返回 this.name 的值。 以下是几种调用  object.getName() 的方式及各自的结果。

```
object.getName(); // My Object

(object.getName)(); // My Object

(object.getName = object.getName)(); // "The Window" 严格模式

```
- 第一个：this.name 就是 object.name
- 第二个：(object.name) 和 object.name 定义相同， 所以依旧返回 My Object
- 第三个：代码先执行了一条赋值语句，然后再调用赋值后的结果。因为这个赋值表达式的值是函数本身，所以 this 的值不可能得到维持，所以返回 "The Window"

### 闭包中的 内存泄漏

如果闭包的作用域链中保存着一个 HTML 元素，那么就意味着该元素将无法被销毁。



{% blockquote %}

参考：

JavaScript 高级程序设计

[Javascript闭包——懂不懂由你，反正我是懂了](http://www.cnblogs.com/frankfang/archive/2011/08/03/2125663.html)

[什么是闭包，我的理解](http://www.cnblogs.com/mzwr1982/archive/2012/05/20/2509295.html)

[什么是闭包，我的理解](http://www.cnblogs.com/xiaotie/archive/2011/08/03/2126145.html)

[js中的闭包之我理解](http://www.cnblogs.com/mzwr1982/archive/2012/05/20/2509295.html)

[如何通俗易懂的解释javascript里面的‘闭包’？](https://www.zhihu.com/question/34547104)

[高效使用 JavaScript 闭包](http://www.ibm.com/developerworks/cn/web/wa-use-javascript-closures-efficiently/index.html)

{% endblockquote %}
