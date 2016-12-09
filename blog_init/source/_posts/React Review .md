---
title: React 优化 
date: 2016-12-07
categories:
- 编程
tags:
- 总结
auto_spacing: true
---

#### 背景

这两天，把自己用 React 的第一项目小小重新改了下。其实我内心是有一点小拒绝的 。。。因为，通过这三个月的练手，这个项目和之后的一个小项目，加上我对第二个小项目重写了大概 3～4 遍，(至今还对它不是很满意 ),  我对 React 的这种思维方式及代码规范收集到了更多的知识。

我是大概看了两三天的 API 然后找到一个实践教程 ( 不知道这么叫对不对...), _Hecking With React_ 。

<!-- more-->

然后就直接开始做项目了，刚开始的时候，我完全不知道从哪里下手，该怎么做，那个时候甚至不知道除了 state 和 props，别的变量在组件内声明是不是一个好的做法。因此，基本上全都用 state，也不知道用 componentShouldMount 来去除避免不需要的 Mount。

导致的结果就是，整个组件的结构都一团糟，全是 state, 无用代码大概有 1/2 到 2/3。

其实我也并不是不想整理，而是时间的问题，我是准备之后用它实践的 Redux 的 ( 现在也就大概能初步上手的样子 )。但是，迫于无奈，我现在必须要稍微整理下 (准备找工作 )。

这次整理，不动整体的组件的结构，只是将 components 的写法规整。


#### 整理过程

首先，因为当时是跟后端对接的，我在项目的最后把本来写在组件里的实例数据全删了。这个项目中，我还不会使用 mock  之类的，在我的电脑上搭了一个 nginx 起了一个服务器来返回数据 ( 里面是后端的代码 )。然后，这两天我用的时候，重新 pull 了一下代码，然后发现，数据只有一两个网页能用，别的都不能用，貌似她后来之后又改过代码。尝试了多次，试了一下去找她的代码，发现看不懂，遂弃。

本来想拿 superagent-mock 做一个简单的数，又考虑希望直接可以放在网上浏览，所以最后决定直接在代码里面放数据 ( 主要放在 container 组件 )，生成静态页面就好了。

然后，我从每个 container 组件入手，主要根据的 fetching 成功后的数据整理，大概将数据还原，然后再一个一个的组件的确认调整。

数据通了我就可以调整代码了，主要的整理的有这几个点：

1. 组件里的自定义 Function ( 方法 ) 都在 constructor 里绑定 this ( .bind(this) )

2. 将大部分的变量 ( 属性 ) 移出 state，同样在 constructor 里声明 ( this.num = 0 之类 )。

3. 其中还有一大部分的 state 来自 props，同样将它们大部分都移出 state。

4. 将组件中用到上述两条中的 this.state.XXX, 改成相应正确调用的形式。

5. 同样由于前两条，很多的 componentWillReceiveProps 和 componentWillUpdate 需要移出。

6. 对于 render 里面很多写 this.state.XXX 和 this.props.XXX 情况，用 ES6解构赋值 const { n1,n2 } = this.state, 这种形式。

7. map 函数中的 function(){}  ( 偶尔还会给后面 加 bind( this ) ), 改成箭头函数，同时去除 bind。

8. 将里面大部分的用 let 声明的常量用 const 声明。

9. 哦对，我之前一直都没有初始化 props, 需要的组件额后面加上 XXX.defaultProps, XXX.propTypes。

10. 将某些 class componennt 改成 function component。

11. 将某些注册监听滚动的组件，在 unmount 的时候解除监听。 ( 监听事件和解除监听事件这里不是很懂 )

12. 将某些组件的 React.Component 改成 React.PureComponent ( 相比于 shouldComponentUnpadte 方便多～ )

嗯，大概这一次就大概改这些吧～ 

更多的优化留给之后啦～


__待优化的方向，__

- 组件细分
- load transtion 之类的，优化效果
- eslint ( 好处多多 )
- 数据 Immutable 
- Redux 用起来！
- CSS module 
- class 命名

其中，Redux 小试了一下，Css module 和 Immutable 都没有用过。


---

标记下： 我在调试的时候，在 Chrome React 的那个调试工具，发现有时候会多出几套 组件，就是相当于把现在的组件全复制了几份，不知道是哪里出的问题。。复现不了。。。




{% blockquote %}
#### 更多组件优化参考

[React移动web极致优化](http://dev.qq.com/topic/579083d1c9da73584b02587d)

[React：创建可维护、高性能的 UI 组件](http://www.ibm.com/developerworks/cn/web/wa-react-intro/)

{% endblockquote %}
 

