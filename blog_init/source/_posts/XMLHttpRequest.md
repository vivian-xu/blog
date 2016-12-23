---
title: XMLHttpRequest
categories:
- 编程
tags:
- 抄抄抄
- XMLHttpRequest
- JS 基础
---


感觉很少仔细看部分，前两天面试的时候有问到，今天重新看下。


Ajax 技术的核心是 XMLHttpRequest 对象( 简称 XHR ),

### 用法

#### 初始化: xhr.open(_method, url, async, user, password_);

- 接收 5 个参数:

  + method: 要发送的请求类型
  + url: 请求的 URL ( 相对路径的话是相对执行代码的当前页面, 也可以是绝对路径 )
  + async: 是否异步发送请求
  + user: 可选。用于身份验证的目的。默认值为 null
  + password:可选。也是用于身份验证的目的。默认值为 null

<!-- more -->
当使用的是_同步_请求的时候，有如下_限制_ ( 否则会报错: _InvalidAccessError_ )：

- xhr.timeout 必须为 0
- xhr.withCredentials 必须为 false
- xhr.responseType 必须为 ""

NOTE:

- 初始化一个请求，如果是本地代码，请使用 __openRequest()__代替

- 在一个已经激活的 request下（已经调用open()或者openRequest()方法的request）再次调用这个方法相当于调用了__abort()__ 方法。

```
xhr.open( 'get', 'example.php', false );
```

#### GET请求

最常用于向服务器查询某些信息。
必要时，可以将查询字符串参数追加到 URL 末尾.

对于 XHR，位于传入 open() 方法的 URL 末尾的查询字符串:
- 必须要使用 __encodeURIComponent()__ 进行编码, 才能放到 URL 的末尾
- 所有__名-值对__ 都必须 & 分隔。
如下：

```
xhr.open("get", "example.php?name1=value&name2=value2", true);

// 这个函数可以辅助向现有 URL 的末尾添加查询字符串参数

function addURLParam( url, name, value ) {
  url += ( url.indexOf("?") == -1 ? "?" : "&" );
  url += encodeURIComponent( name ) + "=" + encodeURIComponent( value );
  return url;
}

```
#### POST 请求

##### open()

- 通常用于向服务器发送应该被保存的数据
- POST 请求应该把数据作为请求主体提交。 send( data )
- POST 请求的主体可以包含非常多的数据, 各式不限。

```
xhr.open( "psot", "example.php", true)
```
##### send()

然后向 send() 方法中传入某些数据。

__默认情况下__， 服务器对 __POST__ 请求和 提交 __Form__ 表单并不会__一视同仁__。所以服务器端必须要有程序来__读取__发送过来的原始数据，并从中__解析__出有用的部分。

我们可以使用 _XHR 模仿_表单提交时的内容类型，其次是以__适当的格式__创建一个字符串。
__POST 数据的格式与查询字符串格式相同__。

```
function submitData() {
  var xhr = new XMLHttpRequest();

  xhr.onreadystatechange = () => {
    if( xhr.readyState == 4 ){
      if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304 ) {
        console.log(xhr.responseText);
      } else {
        console.log("Request was unsuccessful:" + xhr.status);
      }
    }
  }

  xhr.open("POST", "postexample.php", true );
  // 表单提交时的内容类型
  xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded" );
  var form = document.getElementById("user-info");

  // 将页面中的表单数据进行序列化
  xhr.send( serialize( form ) );
}
```

##### FormData 类型

为序列化表单以及创建与表单格式相同的数据提供了便利。

```
// 第 1 种方法
let data = new FormData();
data.append("name", "Nicholas");

// 第 2 种方法
let data = new FormData(document.forms[0]);
```
append 接收 2 个参数: 键和值
- 可以通过 append 添加任意多个键值对。
- 也可以通过向 FormData 构造函数中传入表单元素

创建 FormData 的实例之后， 可以将它直接传给 XHR 的 send()

```
var xhr = new XMLHttpRequest();

xhr.onreadystatechange = () => {
  if( xhr.readyState == 4 ){
    if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304 ) {
      console.log(xhr.responseText);
    } else {
      console.log("Request was unsuccessful:" + xhr.status);
    }
  }
}

xhr.open("POST", "postexample.php", true );
var form = document.getElementById("user-info");
xhr.send(new FormData(form) );

```

#### 发送请求: xhr.send( _data_ )

- 接收 1 个参数：data
  + 作为请求主体发送的数据

- 参数 data 可以是以下几种类型：
  + ArrayBuffer
  + Blob
  + Document
  + DOMString
  + FormData
  + null

- 如果不需要通过请求主体发送数据，需传入 null


NOTE: 如果使用 _GET / HEAD_ 请求，参数 _data_ 会被设置为 _null_

_data_ 参数的_数据类型_会__影响__请求头部的 __content-type__ 的 __默认值__：

- data: __Document ( HTML Document 类型 )__，则 content-type: __text/html__;charset=UTF-8; 否则为 __application/xml__;charset=UTF-8
- data: __DOMString__, content-type: __text/plain__;charset=UTF-8
- data: __FormData__, content-type: __multipart/form-data__;boundary=[xxx]
- data 是其他类型， 则__不会设置__ content-type 的默认值

基本使用：

```
xhr.send();
```

在__断网__状态下调用 xhr.send(data), 会__抛错__：Uncaught NetworkError: Failed to execute 'send' on 'XMLHttpRequest' 。一旦程序抛错误，若不 catch 就无法继续执行后面的代码，所以调用 xhr.send(), 应该用 __try-catch__ 捕捉错误

```
try {
  xhr.send(data)
} catch(e) {
  //do something...
}

```

#### 取消异步请求: xhr.abort()

调用这个方法之后， XHR 对象会停止触发事件，而且也不再允许访问任何与响应有关的对象属性。

在_终止请求_之后，__还应该对 XHR 对象进行接引用操作__。由于内存原因, 不建议重用 XHR 对象。

### 示例

#### 同步请求

- 调用 send() 之后，请求就会被分派到服务器

```
xhr.open( 'get', 'example.php', false );
xhr.send();
```
👆的请求是 _同步_的，JS 代码会 _等到服务器响应之后_再继续执行。

- 收到响应后，响应的 _数据_会自动填充 XHR 对象的属性，相关属性:   ( __具体可以看后面__ )

  - responseText: 作为响应的主体背返回的文本。
  - responseXML: 如果响应的内容类型是 "text / xml" 或者 "application / xml", 这个属性中将保存 _包含着响应数据的 XML DOM 文档_
  - status: 响应的 HTTP 状态
  - statusText: HTTP 状态的说明

- 接收到响应后，第一步是检查 status 属性，以确定响应已经成功返回。

  + 一般来说，可将 HTTP 状态代码为 __200__ 作为成功的标志
  + 此时， responseText 属性内容已经就绪，而且在内容类型正确的情况下， responseXML 也应该能够访问了。

  + 状态代码: __304__表示请求的资源并没有被修改，可以直接使用浏览器中缓存的版本, 当然也表示响应是有效的

  + __为确保接收到适当的响应，应该像👇这样检查上述代码 (_不要依赖 statusText_) ：__

```
let xhr = new XMLHttpRequest();

// 我这里调用的是 github react/forks 的 API
xhr.open("get", "https://api.github.com/repos/facebook/react/forks", false);
xhr.send(null);

if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304 ) {
  console.log(xhr.responseText);
} else {
  console.log("Request was unsuccessful:" + xhr.status);
}
```

结果

![](http://img.jackon.me/xhr1.png)


##### 同步请求 NOTE:
当 xhr 为同步时：

- xhr.timeout: 需为 0
- xhr.withCredentials: 需为 false
- xhr.responseType: 需为 ""
- 无法设置 请求超时 ( xhr.timeout )
- xhr.upload.onprogress , xhr.onprogress 事件不会触发

### HTTP 头部信息 ( 不太能用到 )

每个 HTTP 请求和响应都会带有相应的头部信息( 并不一定都有用 )
XHR 对象也提供了操作这两种头部 ( 即__请求头部和响应头部__ ) 信息的方法。

默认情况下，在发送 XHR 请求的同时，还会发送下列头部信息：

- Accept: 浏览器能够处理的内容类型
- Accept-Charset: 浏览器能够显示的字符集
- Accept-Encoding: 浏览器能够处理的压缩编码
- Accept-Language: 浏览器当前设置的语言
- Connection: 浏览器与服务器之间连接的类型
- Cookie: 当前页面设置的任何 Cookie
- Host: 发送请的页面所在的域
- Referer: 发送请求的页面的 URL。( 这个英文单词应该是 Referrer, 然而 HTTP 规范将这个词拼错了，所以只能讲错就错 )
- User-Agent: 浏览器的用户代理字符串

#### setRequestHeader( _DOMString header, DOMString value_ )

使用 __setRequestHeader()__ 方法可以__设置__请求头部信息__

  + 接受 2 个参数：头部字段的名称， 头部字段的值
  + 要成功发送请求头部的信息，必须在调用 __open() 之后， send() 之前__ 调用 setRequestHeader()
  + 可以多次调用, 最终的值不会采用覆盖 ( override ) 的方式, 而是采用追加 append 的方式。
  + 建议使用 __自定义__头部字段名称，否则可能会影响服务器端的响应

例子：

```
let xhr = new XMLHttpRequest();

xhr.onreadystatechange = () => {
  if( xhr.readyState == 4 ){
    if( (xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
      console.log( xhr.responseText);
    } else {
      console.log("Request was unsuccessful: " + xhr.status);
    }
  }
};

// 我这里调用的依旧是 github react/forks 的 API
xhr.open("get" , "https://api.github.com/repos/facebook/react/forks", true);

xhr.setRequestHeader("MyHeader", "MyValue");
xhr.setRequestHeader("MyHeader", "HerValue");
// 最终的 header 中的 "MyHeader" 为 "MyValue", "HerValue"
xhr.send(null);
```

#### 获取响应头部 ( _safe_ 的字段 )

- getResponseHeader( _DOMString header_ )

返回 __指定的响应的值__，如果响应头还没被接受或者响应头不存在，返回 __null__

```
let myHeader = xhr.getResponseHeader("MyHeader");
```
- getAllResponseHeaders()

返回所有的响应头信息。如果响应头还没有接受，则返回 null.

NOTE: For multipart requests, this returns the headers from the current part of the request, not from the original channel.

```
let allHeaders = xhr.getAllResponseHeaders();
```
NOTE：

- W3C 限制：客户端无法获取 response __Set-Cookie, Set-Cookie2__( xhr 标准 ); __跨域请求时__客户端允许获取 __simple response header,  Access-Control-Expose-Headers__( cors 标准 )

- getAllResponseHeaders() __只能__ 拿到 __限制以外__( 被视为 _safe_ ) 的 header 字段 ( 第一条 )。

- getResponseHeader( _header_ ) 方法时, header 参数必须是 __限制以外__的 header, 否则会报错: _Refused to get unsafe header_。 ( 第一条 )

#### 指定 xhr.response 的数据类型

- 使用 level 1 提供的 __overrideMimeType( )__ 方法

比如：

- 我们想要 server 点返回一个 DOM 对象, 就可以设置 __xhr.overrideMimeType('text/xml, charset = utf-8")__。
- xhr level 1 __不支持__直接传送 __blob__ 二进制数据, 可以利用 __overrideMimeType()__ 方法

如下 level 1 获取 一张图片

```
let xhr = new XMLHttpRequest();

// 获取一张 图片
xhr.open("get" , "http://img.jackon.me/cheese.jpeg", true);

// 将响应的数据按照纯文本来解析，字符集替换为用户自己定义的字符集
xhr.overrideMimeType('text/plain; charset=x-user-defined');

xhr.onreadystatechange = () => {
  if( xhr.readyState == 4 ){
    if( (xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {

      let blobStr = xhr.responseText;
    // 逐个字节的还原成二进制数据, 顺序  blobStr[i] --> ASCII( 16 进制 ) -->  2 进制
      for( let i=0, len = blobStr.length; i<len; ++i ) {
        let c = blobStr.charCodeAt(i)
        let byte = c&0xff;
      }
    } else {
      console.log("Request was unsuccessful: " + xhr.status);
    }
  }
};

xhr.send(null);
```
通过设置 response 的 content-type 为 "text/plain;charset=x-user-defined" ，使 xhr 以纯文本格式解析接收到的 blob 数据, 然后再将其转换为 blob 数据

- xhr.responseType

指定 xhr.response 的数据类型，可以设置如下:

- ""   ( String 字符串, 默认 )
- "text"   ( String )
- "document"   ( Document 对象，返回格式为 XML )
- "json"    ( js 对象， __IE10, IE11 不支持__ )
- "blob"
- "arrayBuffer"

获取图片

```
let xhr = new XMLHttpRequest();

xhr.open("get" , "http://img.jackon.me/cheese.jpeg", true);

// 可使用 blob 或者 arrayBuffer

xhr.responseType = 'blob';
// xhr.responseType = 'arrayBuffer';

xhr.onreadystatechange = () => {
  if( xhr.readyState == 4 ){
    if( (xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
      let blobStr = xhr.response;
      console.log( blobStr )
    } else {
      console.log("Request was unsuccessful: " + xhr.status);
    }
  }
};

xhr.send(null);
```

结果：

![](http://img.jackon.me/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-12-18%20%E4%B8%8B%E5%8D%881.41.54.png)

#### 获取 response 数据

有 3 个属性可以获取返回的数据, 分别是： __xhr.response, xhr.reponseText, xhr.responseXML__

- xhr.response
  + 默认值: ""
  + 请求__完成__时, 才有正确的值
  + 请求未完成时,
    - 当 responseText 为 "" 或 "text" 时, 值为 ""
    - 当 responseText 为 其他值时, 值为 null

- xhr.responseText
  + 默认值: ""
  + 只有当 responseType 为 __"text", ""__ 时, xhr 对象__才有此属性__，此时才能调用 xhr.responseText, 否则 _抛错_
  + 只有当请求__成功__时, 才能拿到__正确值__。当请求__未完成__或者请求__失败__ 时 值都为 __""__

- xhr.responseXML
  + 默认值: null
  + 只有当 __responseType 为 "text", "","document"__ 时, xhr 对象 __才有此属性__, 此时才能调用 __xhr.responseXML__, 否则抛错
  + 只有当请求__成功__时, 才能拿到__正确值__。当请求__未完成__, 请求__失败__, 请求__成功但返回数据无法被正确解析__ 时 值都为 __null__

#### 追踪 ajax 请求的当前状态: xhr.readyState

当发送 __异步__请求，接收响应的时候，可以检测 XHR 对象的 __readyState__ 属性，该属性表示 __请求 ／ 响应过程的当前活动阶段__。这个属性可取的值如下：

  - 0: 未初始化。尚未调用 open() 方法。
  - 1: 启动。已经调用 open() 方法，但是尚未接收到响应。
  - 2: 发送。已经调用 send() 方法，但尚未接收到响应。
  - 3: 接收。已经接收到部分响应数据。
  - __4__: 完成。已经接收到全部响应数据，而且已经可以在客户端使用。

只要 __readyState__ 属性的值__改变__，__都会__触发一次 __readystatechange__ 事件。
可以__利用__这个事件来__检测__每次__状态变化__后 __readyState__ 的值。

NOTE!!!: 必须在调用 _open()_  __之前__指定 __onreadystatechange__ 事件处理程序才能__确保__跨浏览器__兼容性__。

例子:

```
let xhr = new XMLHttpRequest();

xhr.onreadystatechange = () => {
  if( xhr.readyState == 4 ){
    if( (xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
      console.log( xhr.responseText);
    } else {
      console.log("Request was unsuccessful: " + xhr.status);
    }
  }
};

// 我这里调用的依旧是 github react/forks 的 API
xhr.open("get" , "https://api.github.com/repos/facebook/react/forks", true);

xhr.send(null);
```

结果：

![](http://img.jackon.me/xhr.png)

#### 设置请求超时时间: xhr.timeout

从__请求开始__算起, 若超过 timeout 时间请求还没有结束,  则会触发  ontimeout 事件, 主动结束该请求

请求开始: xhr.onloadstart 事件触发的时候 ( 也就时调用 xhr.send() 的时候 )

请求结束: xhr.loadend 事件触发的时候

- 单位: 毫秒
- 默认值：0

 NOTE:
 - 在 __send() 之后__ 再设置 timeout, 但__计时起点__ 仍旧为调用 __xhr.send()__ 方法的时刻
 - 当 时一个 __同步__ 请求时, xhr.timeout 必须设置为 0, 否则__抛错__

### 进度事件

有 6 个进度事件:

- loadstart: 在接收到响应数据的第一个字节时触发
- progress: 在接收响应期间持续不断触发
- error: 在请求发生错误时触发
- abort: 在因为调用 abort() 方法而终止连接时触发
- load: 在接收到完整的响应数据时触发
- loadend: 在通信完成或者触发 error, abort, load 事件中的一个

每个请求都从触发 __loadstart__ 事件开始, 接下来是 __一个或者 多个__
__progress__ 事件，然后以触发 __loadend__ 事件结束

NOTE:
- __lE8+ 只支持 load 事件。__
- 没有浏览器支持 loadend 事件

#### load 事件

只要浏览器接收到服务器的响应，__不管其状态如何__，都会触发 load 事件。所以每次 __必须__ 检查 __status__ 属性, 才能__确定__数据是否真的已经可用。 Firefox, Opera, Chrome, Safari 都支持 load 事件

```
let xhr = new XMLHttpRequest();

xhr.open("get" , "http://img.jackon.me/cheese.jpeg", true);

xhr.responseType = 'arrayBuffer';

xhr.onload = () => {
  if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
    console.log(xhr.responseText );
  } else {
    console.log("Request was unsuccessful: " + xhr.status);
  }
}
xhr.send(null);
```

#### progress 事件
 这个事件会在浏览器接收新数据期间周期性地触发，默认情况下这个事件__每 50ms__ 触发一次。

 __onprogress__ 事件处理程序会接收一个 __event__ 事件, 其 __target 属性__ 是 XHR 对象, 但包含着三个额外的属性:

- lengthComputable: 表示进度信息是否可用的布尓值
- position: __已经接收__的字节数
- totalSize: 表示 根据 Content-length 响应头部确定的预期字节数.

_上传，下载过程触发的是不同对象的 onprogress 事件_:

- 上传: xhr.upload 对象的 onprogress 事件
- 下载: xhr 对象的 onprogress 事件

必须在调用 __open()__ 方法__之前__添加 onprogress 事件处理程序
```
let log = document.querySelector("#log")

function downProgress(e) {
   log.innerHTML = `Received <strong>${e.position}</strong> of <strong>${e.totalSize}</strong> bytes`
}

let xhr = new XMLHttpRequest();

xhr.onprogress = (e) => {
  console.log( 'onprogress' )
  if( e.lengthComputable ) {
    downProgress(e)
  }
}

xhr.onload = () => {
  if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
    console.log(xhr.responseText );
  } else {
    console.log("Request was unsuccessful: " + xhr.status);
  }
}

xhr.open("get" , "http://img.jackon.me/cheese.jpeg", true);

xhr.responseType = 'arrayBuffer';

xhr.send(null);
```

结果;

![](http://img.jackon.me/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-12-18%20%E4%B8%8B%E5%8D%886.54.00.png)


{% blockquote %}

参考：

[你真的会使用 XMLHttpRequest 吗？](https://segmentfault.com/a/1190000004322487)

_JavaScript 高级程序设计_
{% endblockquote %}

