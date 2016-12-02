---
title: Web 存储

---

### 目的

- 提供一种在 cookie 之外存储会话数据的途径。

- 提供一种存储大量可以跨会话存在的数据机制。


#### 类型

最初的 Web Storage 规范包含了两种对象的定义： sessionStorage 和 globalStorage。 它们是以 window  的对象属性的形式存在的。

##### Storage 类型

  Storage 类型提供最大的存储空间, 来存储名值对。( 只能存储字符串，非字符串会在存储前转换成字符串 )

  Storage 的实例与其他对象类似，有如下方法。

  + clear(): 删除所有值; Ff 中没有实现

  + getItem(name): 根据指定的名字 name 获取对应的值

  + key(index): 获取 index 位置处的值的名字

  + removeItem(name, value): 删除由 name 指定的名值对

  + setItem(name, value): 为指定的 name 设置一个对应的值

其中， getItem(), removeItem(), setItem() 方法可以直接调用，也可以通过 Storage 对象间接调用。

因为每个项目都是作为属性存储在该对象上的，所以可以通过 点语法 或者 方括号 访问属性来读取，设置值，或 通过 delete 来删除值。 ( __不建议__ )

我们还可以用 __length__ 属性来判断有多少名值对，但无法判断所有数据的大小。( IE8 提供了 remainingSpace 属性，用于获取还可以使用的存储空间的字节数 )

##### sessionStorage 对象 ( Storage 的一个实例 )

  + 存储特定于每个会话的数据 ( 即该数据只保持到 __浏览器__ 关闭 )

  + 可以跨越页面刷新而存在 ( __不是跨越会话__ )

  + 如果浏览器支持，浏览器崩溃重启之后依然可以使用 ( Ff 和 Webkit 支持, IE 不行 )

  + sessionStorage 对象绑定于某个服务器会话。( 文件本地运行不可用 )

  + 存储在它里面的数据只能由最初给对象存储数据的页面访问到，所以对 __多页面__ 应用有限制

  + 由于它是 Storage 的一个实例，所以可以用 setItem() 或者直接设置新的属性来存储数据

```
  // 使用方法存储数据
  sessionStorage.setItem("name", "Nicholas");

  // 使用属性存储数据
  sessionStorage.book = "Professional JavaScript";

```
  + 不同浏览器写入数据方面略有不同。Ff 和 Webkit 同步写入， IE  异步写入。 所以，IE 在设置数据和将数据实际写入磁盘之间可能会有一些延迟, 但是它会更快的回复执行。

  + IE8 中可以强制把数据写入磁盘： 在设置新数据前使用 begin() , 在所有设置完了后调用 commit() 。

```
  sessionStorage.begin();
  sessionStorage.name = "Nicholas";
  sessionStorage.book = "Professional JavaScript";
  sessionStorage.commit();
```

这样就确保 name book 在调用 commit() 后被立刻写入了磁盘。 begin() 是确保在这段代码执行的时候不会有其他磁盘写入操作。

  + 读取数据可以使用 getItem() 或 通过直接访问属性名。

```
// 使用方法获取
var name = sessionStorage.getItem("name");

// 使用属性读取
var book = sessionStorage.book;
```
  + 迭代
    - 通过 length 和 key()

```
  for(var i = 0, len = sessionStorage.length(); i < len; i++) {
    var key = sessionStorage.key(i);
    var value = sessionStorage.getItem(key);
    alert(key + "=" + value);
  }
```
    - 通过 for  in
```
  for (var key in sessionStorage) {
    var value = sessionStorage.getItem(key);
    alert(key + "=" + value);
  }
```
  + 删除

    - delete 操作符

```
// Webkit 中无效
  delete sessionStorage.name
```

    - removeItem()

```
// removeItem()
  sessionStorage.removeItem('book');
```

##### globalStorage 对象

    - 目的是实现跨越会话的存储数据，但是有特定的访问权限

    - 使用之前先指定哪些域可以访问该数据。( 通过方括号标记使用属性实现 )

```
// 保存数据
globalStorage["wrox.com"].name = "Nicholas";

// 获取数据
var name = globalStorage["wrox.com"].name;
```
上面是针对 wrox.com 的存储空间。对于  wrox.com 和 它的子域都是可访问的

    - globalStorage 对象 __不是  Storage 的实例__
    - globalStorage["wrox.com"] 才是

    - 可以这样限制只有 子域名 www.wrox.com 可访问

```
// 保存数据
globalStorage["www.wrox.com"].name = "Nicholas";

// 获取数据
var name = globalStorage["www.wrox.com"].name;
```

  - 对于 globalStorage
  空间的访问，是依据发起请求的页面的 __域名，协议，端口__ 来限制的。

  比如：

    通过 HTTP 不能访问 通过 HTTPS 存储的数据

    端口不同，不能访问

  - __globalStorage 的每个属性都是 Storage 的实例__

    可以这样子：

```
globalStorage["www.wrox.com"].name = "Nicholas";
globalStorage["www.wrox.com"].book = "Professional JavaScript";
globalStorage["www.wrox.com"].removeItem("name");

var book = globalStorage["www.wrox.com"].getItem("book");
```

  - 当不能确定域名的时候 用 location.host

  - 若不使用 removeItem() / delete 删除，或 用户未清除缓存， globalStorage 里的数据会一直保留在磁盘上

##### localStorage

  localStorage 在 HTML5 规范中作为 持久保存 客户端数据的方案取代了 globalStorage
---

  - localStorage 不能指定任何访问规则。

  - 要访问同一个 localStorage 对象， 页面必须来自同一个域名，使用同一个端口，在同一个协议下 ( 相当于 globalStorage[location.host] )

  - localStorage 是 Storage 的实例，可以像使用 sessionStorage 一样来使用它

```
  // 使用方法存储数据
  localStorage.setItem("name", "Nicholas");

 // 使用属性存储数据
  localStorage.book = "Professional JavaScript";

  //  使用方法 读取数据
  var name = localStorage.getItem("name");

  // 使用属性读取数据
  var book = localStorage.book;
```
  - 存储在 localStorage 中的数据和存储在 globalStorage 中的数据一样， __数据保留到通过 JS 删除，或者用户清除缓存  __

##### storage 事件

  对 storage 对象进行任何修改，都会在文档上触发 storage 事件。

  这个事件的 event 对象有以下几个属性

    + domain':' 发生变化的存储空间的域名
    + key'':'' 设置或者删除的键名
    + newValue':' 如果是设置值，则是新值; 如果是删除键，则是 null
    + oldValue':' 键被更改之前的值

  其中 IE8 和 Ff 只实现了 domain 属性。 webkit 尚不支持 storage 事件。

##### 限制

  - 因浏览器而异

  - 以每个 来源( 协议，端口，域 ) 为单位 ( 每个来源都有固定的空间 )

  - localStorage 多数为 5MB 限制， Chorme 和 Safari 是 2.5MB 限制。 IOS 版的 Safari , Android 版 Webkit 也是 2.5MB。

  - sessionStorage， 有的没有限制，  Chorme 和 Safari , IOS 版的 Safari , Android 版 Webkit 也是 2.5MB。
    IE8+ 和 Opera 是 5MB

--
参考 :

  JavaScript 高级程序设计
