---
title: React-Router 一点一点深入
categories:
- 编程
tags:
- React
- 总结
auto_spacing: true
---

最近一直在使用 React 开发页面，所以对 React 相关的文章什么的都留意了些。在查资料的时候经常看到关于 React 服务端渲染这个话题，但是都没有去看文章，只不过心里一直有疑问，为什么需要服务端渲染，

<!-- more -->

#### SEO

后来知道因为所有内容都是写在 React 里面，即 js 里面的，需要 js 去填充主要的内容到 index.html，而 js 加载是在 index.html 已经渲染出来了之后才执行的事。
而爬虫们呢，一般只会爬初始的 html 页面，所以导致爬虫们什么关键的内容都爬不到，因此 SEO 不好。

所以可以选择，服务器端渲染，然后将一个完整的 html 页面给 浏览器端使用。

#### 路由

然后，昨天知道第二个原因。

就是路由相关。
我昨天再浏览自己做的 demo 网页的时候，突然发现我一刷新返回了 404，本来以为是网络什么的问题，分别拿电脑，别人的手机，别的网页都试了几次，发现的确一直是这个样子的，于是我疑惑为什么？

刚开始稍微想了下，单页应用，就是只有一个页面，具体的 url 匹配操作应该是 react-router 在做的事。想起来刚开始配 webpack-dev-server 的时候，就是只有主页面能请求到东西，后来把 server 的 historyApiFallback 设置为 true，就可以了。

然后在 google 上找相关资料，找到了 stackoverflow  的这个 [React-router urls don't work when refreshing or writting manually](http://stackoverflow.com/questions/27928372/react-router-urls-dont-work-when-refreshing-or-writting-manually)

然后这个最赞的回复我觉得讲得挺清楚的：

##### 原因

出现的原因就是过去只有 server 来解析 url , 而现在有 2 个了。

当用户通过 http://example.com/about 来访问页面的时候，
过去的请求一个 url 的时候，直接是发给 server 端的，由 Server 来解析 URL 中的路径 /about，然后判断该返回哪个页面给 用户，之后将页面返回。

现在由 React-router 给提供的客户端渲染，事情简单了很多。

- 第一步：刚开始的时候，客户端没有任何 JS 代码，所以第一个请求 ( http://example.com )一定是发给 __Server__ 的，然后 Server 返回一个页面，页面中含有__请求 React 和 React-router__ 的 script 标签。
然后，当请求到了 React /React-router 时候就开始了第二步。

- 第二步：假设用户点击了 '__About us__' 这个链接 ，只改变了__本地 URL__到 http://example.com/about ( 利用 __History API __), 而__没有__向 服务器端发任何请求，而是 __React-router __替代Server这个工作，它解析了 URL 中的路径，判断哪个 Resct view 去 渲染, 然后渲染它。假设 about 页面不需要任何 rest 调用，你已经完成了。 这样 不需要任何 的 服务端调用， 页面从 Home 到  About us。

所以当你__点击__一个链接的时候，运行的 JS 操纵地址栏中的 URL, 它不会让页面__重新刷新__，而是让 __React-router__ 去执行客户端的页面转换。

现在考虑一下，当你复制这个 URL ( http://example.com/about ) 给你朋友 ，她粘贴 URL 到地址栏上的时候。

这个时候她相当于还在__第一步__，因为没有下载网页，也没有 React-Router 运行。所以会向 __Server__ 发送请求访问。

这里就是问题所在。现在，你只是放了一个静态 HTML 在你 server 的网站根目录中，但是当请求访问其他 URLs 的时候 server 会返回 404。

同样的 URLs 访问正常是因为 React-router 做了这些路由的工作，但是 Server 肯定失败，除非__你能让 Server 端也能解析这些 URL__。


##### 解决方法

主要是这几种:

- 完全绕开这个问题 ( Hash History )

- 设置 Server 端所有 URL ( /* ) 都返回 相同的 index.html ( 通过使用 Browser History并且设置 Server 返回 index.html ),

- 在特定的路由上增加一些脚本，( 用简单的 PHP 返回一些 有内容的重要的页面。。__这个不懂__  )( Hybrid 混合方式？ )

- 在 Server 端 ( Node.js ) 和客户端运行同样的 JS ( Isomorphic )

#### 我的解决方法

因为，我们 Server 端是 __nginx__ ，所以选择了第二种方法。

本来是想让 男票搞定的，但是，我们两对 nginx 的语法都不是很熟，所以我搜了一个发现这个 [react-router/docs/guides/Histories.md](https://github.com/ReactTraining/react-router/blob/master/docs/guides/Histories.md#configuring-your-server)。
然后照着那个一设置，OK 了！
---
##### 牛逼闪闪的男票补充：

但是，所有的都返回 index.html 能用，但是不太安全，也不太合理。

只有用户刷新／直接打开其他页面的时候，才会出问题。大部分页面，用户不会直接输入url 打开的。

所以，只把用户会输入 url 直接打开的页面，配一下就好了。

也就是在这几个特定的路由上增加一些脚本，让他们直接返回 index.html, 而这个特定的路由的概念，又可以扩展为 所有有效的 URL

而URL ( /* )  匹配的时候，有效无效的，都会匹配上，这是不合理的。

我怎么就没有想到呢···· (￣∇￣)

---
虽然，这个问题解决了，但是还是对 SEO 不好的。以后肯定会用上 __Isomorphic__ 的方案的，嗯，我就先占个坑吧，毕竟，Node.js 还没有学会。。。。


{% blockquote %}
参考：

[React-router urls don't work when refreshing or writting manually](http://stackoverflow.com/questions/27928372/react-router-urls-dont-work-when-refreshing-or-writting-manually)

[react-router/docs/guides/Histories.md](https://github.com/ReactTraining/react-router/blob/master/docs/guides/Histories.md#configuring-your-server)

[从 React Router 谈谈路由的那些事](http://stylechen.com/react-router.html)
{% endblockquote %}


