---
title: 我的 hexo 搭建过程记录
categories:
- 编程
tags:
- 记录
- Hexo
auto_spacing: true
---

#### hexo 基础搭建

- 安装 hexo

    $ npm install -g hexo-cli

- 随便建一个目录

    mkdir hexo_test

- 初始化文件夹 ( 在hexo_test 外面 )

    hexo init hexo_test

- 进入 hexo_test

    cd hexo_test

- npm 初始化

    npm install

- 然后就可以看到有许多文件夹

<!-- more -->

- 生成静态页面

    + hexo generate（hexo g也可以）

    + 在 public 某个目录里( 默认是现在的时间) 有一个新生成的 yourpostname.html 文件。

- 启动本地服务，进行文章预览调试

    hexo server

- 打开 http://localhost:4000

- 注意 ！ 这里我有个错误提示

```bash
  {[Error: Cannot find module './build/Release/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
  {[Error: Cannot find module './build/default/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
  {[Error: Cannot find module './build/Debug/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
```
先查到用这个 npm install hexo --no-optional,
但是对我没用

__(至今还未解决)__

- 新添加一篇 post

    + hexo new "Hey"

    + 在 hexo_test/source/_post 中生成 一个为 Hey.md 的文件。

- 如果你没有起 server 的话，可能需要执行 hexo generate 来生成静态页面

- 再去刷新一下页面，发现页面上新增了 Hey 这个 post，但是没有内容。

- 进入source/_post/Hey.md 你就可以编辑这篇文章的内容了

    + 然后保存， 你就可以在网页中看到你文章的内容了

#### 部署到 github.io 上

- 在根目录下找到 _config.yml 文件, 并打开

- 找到 这部分(在最后)
```
  # Deployment
  ## Docs: https://hexo.io/docs/deployment.html
  deploy:
    type:
```

- 修改上述地方

```
  # Deployment
  ## Docs: https://hexo.io/docs/deployment.html
  deploy:
    type: git
    repository: git@github.com:vivian-xu/vivian-xu.github.io.git
    branch: master
```
##### 注意!
  - repository：必须是SSH形式的url (如上)，而不能是HTTPS形式的url（https://github.com/***/***.github.io.git），
  - 如果你使用SSH url，但是电脑没有开放SSH 端口，会致部署失败。
  - 如果你是为一个项目制作网站，那么需要把branch设置为gh-pages。 ( __看到这个，我把我的 branch 改成 gh-pages了__ )

 - 然后 hexo deploy

    ##### 报错 ERROR Deployer not found: git

    解决方法： npm install hexo-deployer-git --save

  - hexo deploy  成功！

  - 然后我去 github 我的 vivian-xu.github.io.git 的仓库里切换了，分支看了一下，有传进来的！

  - 然后我去访问 vivian-xu.github.io.git/hexo_test/

  结果 404 ....

  于是我在网上搜索问题, 先是找 gh-pages 相关，但是没解决我问题。之后我又去 git 仓库看了下，确保没有什么问题，看着我的 gh-pages 分支，很是疑惑，东西都在，怎么能访问不了。

  看着 gh-pages 我有些疑虑文档里所说的 gh-pages 名字是我建的 文件夹名么。。。 我的文件夹在本地，而且文件夹名没有一起更新到 github 的 vivian-xu.github.io 的 gh-pages 中，github 怎么会知道我的文件夹名。。

  然后我在 hexo 的 [这个issue](https://github.com/hexojs/hexo/issues/350) 里看到回答。

{% blockquote %}
    _yourname.github.io形式的库，会解析master分支；而别的名字的库，会解析gh-pages分支，以yourname.github.io/库名的形式访问。 _
{% endblockquote %}

  这个更加让我相信是名字的问题，于是，我重新建了一个库，然后把 _config.yml 的 git 相关的文件改了

  ```
  type: git
  repository: git@github.com:vivian-xu/hexo_test.git // 新库地址
  branch: gh-pages
  ```
  - 然后重新生成，deploy，检查了下新的 git 的仓库确保文件传了进去

  - 之后打开 vivian-xu.github.io/hexo_test/  已经不再是 404 了！但是。为什么没有样式。。

  - 打开 chorme 的调试工具，发现 css js 的路径都不对( 类似这样的 https://vivian-xu.github.io/fancybox/jquery.fancybox.css )，应该是 https://vivian-xu.github.io/hexo_test/*** 。于是，又去看了 issue ，看到这个 https://github.com/hexojs/hexo/issues/1121

   意识到自己要配 root ( 自己的网站还要配 CNAME )

  于是又修改了 _config.yml

   ```
   url: http://yoursite.com
   root: /hexo_test/
   permalink: :year/:month/:day/:title/
   permalink_defaults:
   ```

  - 再次 hexo g -d ，打开页面 终于成功了！！

  现在是这个样子
  ```
  # Hexo Configuration
  ## Docs: https://hexo.io/docs/configuration.html
  ## Source: https://github.com/hexojs/hexo/

  # Site 站点
  title: Vivian
  subtitle:
  description: Vvian's blog
  keywords:
    - HTML
    - JavaScript
    - CSS
    - React
  author: Vivian
  language: zh-CN #语言
  timezone: Asia/Shanghai #时区

  # URL
  ## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
  url: http://yoursite.com #用于绑定域名, 其他的不需要配置
  root: /hexo_test/
  permalink: :year/:month/:day/:title/
  permalink_defaults:

  # Directory
  source_dir: source
  public_dir: public
  tag_dir: tags #标签
  archive_dir: archives  #归档
  category_dir: categories #分类
  code_dir: downloads/code
  i18n_dir: :lang
  skip_render:

  # Writing
  new_post_name: :title.md # File name of new posts
  default_layout: post #默认模板(post page photo draft)
  titlecase: false # Transform title into titlecase  #标题转换成大写
  external_link: true # Open external links in new tab #新标签页里打开连接
  filename_case: 0
  render_drafts: false
  post_asset_folder: false
  relative_link: false
  future: true
  highlight:  #语法高亮
    enable: true
    line_number: true #显示行号
    auto_detect: false
    tab_replace:

  # Category & Tag
  default_category: uncategorized #默认分类
  category_map:
  tag_map:

  # Date / Time format
  ## Hexo uses Moment.js to parse and display date
  ## You can customize the date format as defined in
  ## http://momentjs.com/docs/#/displaying/format/
  date_format: YYYY-MM-DD
  time_format: HH:mm:ss

  # Pagination
  ## Set per_page to 0 to disable pagination
  per_page: 10
  pagination_dir: page

  # Extensions
  ## Plugins: https://hexo.io/plugins/
  ## Themes: https://hexo.io/themes/
  theme: landscape
  # theme: ninja

  # Deployment
  ## Docs: https://hexo.io/docs/deployment.html
  deploy:
    type: git
    # repository: git@github.com:vivian-xu/vivian-xu.github.io.git
    repository: git@github.com:vivian-xu/blog.git
    branch: gh-pages

  ```

#### theme 主题

- 挑选一个喜欢的 theme, git clone theme/ theme_name 下

- 将网站的 _config.yml 的 theme 改成你要的换的主题的名字

- 根据主题的文档，修改 _config.yml ( 包括网站的和主题的 )

- 生成网页并且 开启 server 调试下，然后自己不满意的地方，手动改改模版。

#### 管理源代码
经某人提醒，我的源代码没有管理，所以又开了一个库用来管理这一整套的源代码。然后想直接在主页面上展示博客首页就好了，于是将 branch 变成了 master。 再次生成，deploy。

#### 部署到自己的域名上
- 因为，如果直接将我的域名，解析道 git 的这个网页的话，要改 CNAME (  只能有一个 )，而我的邮箱还是指向原来服务器的，感觉不太好，于是就保持原来的方式了。直接将静态文件 copy 到服务器下保存。

- 用了一套简单的 CI 系统，将 GitHub 设置成有新的 push 的时候发消息给 CI, CI 自动 pull 下来代码，然后测试，到服务器指定的文件夹里。

到这，基本上就结束了。当然，我觉得，我会没事改改这个 theme 的代码的～～


#### hexo 部分指令

指令 | 简写 | 作用
--|--|--
hexo new "postName" | hexo n "postName" |  #新建文章
hexo new page "pageName" | hexo n  page "postName"  |  #新建页面
hexo generate | hexo g |  #生成静态页面至public目录
hexo server | hexo s | #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo deploy | hexo d | #将.deploy目录部署到GitHub
hexo help | \ | # 查看帮助
hexo version | \  |  #查看Hexo的版本

{% blockquote %}

参考 :

[Hexo常见问题解决方案](https://xuanwo.org/2014/08/14/hexo-usual-problem/#Deploy之后，页面长时间404)

[Hexo 文档](https://hexo.io/zh-cn/docs/)

[Hexo搭建Github静态博客](http://www.cnblogs.com/zhcncn/p/4097881.html)

{% endblockquote %}
