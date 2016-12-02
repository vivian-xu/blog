---
title: Cookie
---
### 数据存储 Cookie

一. Cookie ( HTTP Cookie )

#### 用法

- 该标准要求服务器任意 HTTP 请求发送 Set-Cookie HTTP 头作为响应的一部分，其中包含会话信息。

```
//  服务器的响应头文件
HTTP/1.1 200 OK
Content-type: text/html
Set-Cookie: name=value
Other-header: other-header-value
```

- 浏览器会存储这样的会话信息，并在这之后，通过为每个请求添加 Cookie HTTP 头将信息发送回服务器。
-
```
  GeT/index.html HTTP/1.1
  Cookie: name=value
  other-header: other-header-value
```

#### 限制

- cookie 在性质上是绑在特定的域名下的。 设定了一个 cookie 之后，再给创建它的域名发送请求时。都会包含这个 cookie.
- 但是由于 它是 存在客户端的计算机上的，每个域的 cookie  总数有限。 不过浏览器之间不同。

IE6-  -- 20, IE7+ -- 50,

Ff -- 50, Opera -- 30, Safari / Chorme 没有规定

当超过限制的时候，浏览器会清除之前的设置的 cookie。

- 对 cookie  的尺寸也有限制。

大多数浏览器都有大约  4096B  的长度限制。
 尺寸限制影响到一个域下所有的 cookie ，而并非每个 cookie 单独限制。当超过限制的时候，该 cookie 会被悄无声息的丢掉。

 #### 构成

 - 名称： 唯一确定 cookie 的名称。 不区分大小写。（ 实践中看成区分大小写的，因为有的服务器会这样处理 ） 。 必须是经过 URL 编码的。
 - 值 。 要经过 URL 编码。
 - 域： cookie 是对哪个域是有效的。
- 路径： 对于制定域中的那个路径，应该向服务器发送 cookie 。
- 失效时间。
- 安全标志： 指定后, cookie 只有在使用 SSL 链接时候才发送到服务器。

#### ！！注意！！ 这些参数不会作为发送到服务器的 cookie 信息的一部分，只有明值对才会被发送。

使用分号 + 空格分割每一段

```
HTTP/1.1 200 OK
Content-type: text/html
Set-Cookie: name=value; domain=.wrox.com; path=/; secure
Other-header: other-header-value
```

--
参考 :

  JavaScript 高级程序设计
