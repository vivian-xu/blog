---
title: I love my wwork
date: 2016-12-01 11:08:40
tags:
---
第一次用这个玩意，好想蛮好玩的。

### hexo 试用过程

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

    文件夹名 | 文件夹用途
  -- | --
  _config.yml       |  配置文件
  package.json    |  node 开发环境的配置文件
  scaffolds           |  模版文件夹。当您新建文章时，Hexo 会根据 scaffold 来建立文件。
  source  | 资源文件夹是存放用户资源的地方。除 _posts 文件夹之外，开头命名为 _ (下划线)的文件 / 文件夹和隐藏的文件将会被忽略。Markdown 和 HTML 文件会被解析并放到 public 文件夹，而其他文件会被拷贝过去。
   public / _drafts  | he
   public / _posts  | je
   themes  | 主题 文件夹。Hexo 会根据主题来生成静态页面。


- 生成静态页面

    + hexo generate（hexo g也可以）
    
    + 在 public 某个目录里( 默认是现在的时间) 有一个新生成的 yourpostname.html 文件。

- 启动本地服务，进行文章预览调试

    hexo server

- 打开 http://localhost:4000

- 新添加一篇 post

  + hexo new "Hey"

  + 在 hexo_test/source/_post 中生成 一个为 Hey.md 的文件。

- 如果你没有起 server 的话，可能需要执行 hexo generate 来生成静态页面 

- 再去刷新一下页面，发现页面上新增了 Hey 这个 post，但是没有内容。

- 进入source/_post/Hey.md 你就可以编辑这篇文章的内容了

    + 然后保存， 你就可以在网页中看到你文章的内容了

### 部署到 github 上

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
      type: github,
      repository: git@github.com:vivian-xu/vivian-xu.github.io.git,
      branch: master,
    ```

    ##### 注意!
      - repository：必须是SSH形式的url (如上)，而不能是HTTPS形式的url（https://github.com/***/***.github.io.git），
      
      - 如果你使用SSH url，但是电脑没有开放SSH 端口，会致部署失败。
      
      - 如果你是为一个项目制作网站，那么需要把branch设置为gh-pages。 (  _未实践_ )

<!--more-->
