---
title: webpack 配置
categories:
- 编程
tags:
- 记录
- 存档
- 环境
---

这些都是我以前记在 __有道云笔记__上的，搬过来～～
--

跟 webpack 抗战了很久，直到最近的一次才觉得自己入门了，终于完整的配成功了一次！！ 虽然我知道以后还是后奉献进去更多的时间，但是至少，进步了！

每次配 webpack 的时候都是配到我想砸电脑。 ◡ ヽ(`Д´)ﾉ ┻━┻　 一把心酸泪啊～

<!-- more -->

#### 目录结构

 - root
    + build                        发布目录，发布的时候打包成的文件放这里～
    + dist                          开发目录，开发过程中打包成的文件放这里～
    + src
        - mock/                   mock 设置以及一些 模拟的数据
        - commons/            公用的 js，
          + utils/
          + mock.js              superagent-mock 与 superagent 链接的地方
        - components/        react component 一般的组件
        - styles/                  最终输出的样式文件集合
        - imgs/
        - fonts/
        - pages/                  页面组件 ( container component ), 一般这里 fetch 分发给子组件
        - index.html
        - index.js (入口)       webpack 打包的入口文件
        - routes.js                react-router 路由结构



记录一下现在的配置：

#####  基础配置

```
var path = require('path');
var webpack = require('webpack');
// 自动生成 html 到 dist 文件里，当然还是要有模版的。。会自动添加你要引到 html 中的文件， 可以手动，也可以自动，默认是把webpack 设置打包出去的都引入。还可以自动给文件加上 hash。
var HtmlWebpackPlugin = require('html-webpack-plugin');
var ExtractTextPlugin = require("extract-text-webpack-plugin");

const ROOT_PATH = path.resolve(__dirname);
const src = path.resolve(ROOT_PATH, 'src');
const dist = path.resolve(ROOT_PATH, 'dist');
const PORT = 7070;

module.exports = {
/*
  现在的代码是合并以后的代码,这样出错以后就会采用source-map的形式直接显示你出错代码的位置。
*/
  devtool: 'eval-source-map',
  // 这个是和 webpack-dev-derver 结合起来用的
  entry: [
    'webpack-dev-server/client?http://localhost:' + PORT,
    'webpack/hot/only-dev-server',
    './src/index.js'
  ],

// 这个是结合 express 的用法
  entry: [
    'webpack-hot-middleware/client?reload=true',
    './src/index.js'
  ],

  output: {
    // 生成的文件放置的地方
    path: dist,
    publicPath: '/static/', //模板、样式、脚本、图片等资源对应的server上的路径
    // 生成的主文件的名字
    filename: 'bundle.js'
    // chunkFilename: 'js/[id].bundle.js'   //dundle生成的配置
  },

  module: {
  //先进行下检查
    preLoaders: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        include: /src/,
        loader: 'eslint'
      }
    ],
    loaders: [{
        test: /\.js?$/,
        exclude: /node_modules/,
        include: [src, './mock'],
        loader: 'react-hot!babel' //react 热替换
      },
      {
        test: /\.scss$/,
           // 将 css 单独打包出来
        loader: ExtractTextPlugin.extract('style-loader', 'css?sourceMap!postcss!sass?sourceMap')
      }, {
        test: /\.css$/,
        // 将 css 单独打包出来
        loader: ExtractTextPlugin.extract('style-loader', 'css'),
      }, {
        test: /\.(woff|woff2|ttf|eot|svg)\??.*$/,
        loader: 'url-loader?limit=8192&name=./fonts/[name].[ext]'
      }, {
        test: /\.(png|jpe?g|gif)$/,
        loader: 'url-loader?limit=8192&name=./imgs/[name].[ext]'
      }
    ]
  },
  // 编译 css 的时候加上 autoprefixer
  postcss: function () {
    return [require('autoprefixer')];
  },
  // 这里注意 第一个 参数是 '' ,里面没有空格
  resolve: {
    extensions: ['', '.js', '.jsx']
  },

  plugins: [
  // production 环境下加  hash

    new ExtractTextPlugin('./css/style.css'),
    // 热替换
    new webpack.HotModuleReplacementPlugin(),
     dev 时候有错误提示，但不会自动停掉 编译 /server 服务
    new webpack.NoErrorsPlugin(),
    // 全局变量，这样在用 React 的时候就不用每一个文件前面都加 import 了。
    new webpack.ProvidePlugin({
      $: "jquery",
      jQuery: "jquery",
      "window.jQuery": "jquery",
      "React": "react"
    }),
    new HtmlWebpackPlugin({
      filename: './index.html',
      template: './src/index.html',
      title: 'Job',
      inject: true,
      hash: true,
    }),
  ]
};

```



##### server 的配置 ( 开发时候用 )

- 基于 express

```
// in server.js
var webpack = require('webpack');
var config = require('./webpack.config.js');

var webpackDevMiddleware = require('webpack-dev-middleware');
var webpackHotMiddleware = require('webpack-hot-middleware');

const port = '7070';
var app = new(require('express'))();

var compiler = webpack(config);
app.use(webpackDevMiddleware(compiler, {
  noInfo: true,
  publicPath: config.output.publicPath, // 与 output.publicPath 一定要一致
  historyApiFallback: true,
  stats: {
    colors: true,
  },
}));
app.use(webpackHotMiddleware(compiler));

app.get('/', function(req,res) {
  console.log('get /');
  res.sendFile(__dirname + '/dist/index.html');
});

app.listen(port, '0.0.0.0', function (err, result) {
  if (err) {
    console.log(err);
  }
  // console.log('Listening at localhost:7070/');
  console.info("==> 🌎  Listening on port %s. Open up http://localhost:%s/ in your browser.", port, port)
});

// in package.json
// --content-base 页面／文件的 rootPath
// --config 以这个 js 文件作为配置文件

    "dev": "webpack-dev-server --content-base=/dist --config server.js --progress --profile --colors",

    "build": "rm -rf ./dist/* && webpack --config webpack.prod.config.js --progress --profile --colors --display-error-details",

```



- dev-serve版本

```
// webpack-dev-serve 是一个小型的用 Express 搭建成的 serve.
// 用 webpack-dev-middleware 来 serve webpack bundle 的。（ webpack-dev-middleware 是一个简单的包装webpack的中间件，可以 watch 我们需要打包的文件，每次更新，打包成新的 bundle 都会通过它发出给serve。它将文件都放在内存里，而不是磁盘中。

 // in serve.js
var webpack = require('webpack');
var WebpackDevServer = require('webpack-dev-server');
var config = require('./webpack.config.js');

var compiler = webpack(config);
const API_TARGET = "http://wuguishifu.com";

var server = new WebpackDevServer(compiler, {
  publicPath: config.output.publicPath,
   // Can also be an array, or: contentBase: "http://localhost/",
  contentBase: './dist',
  inline: true,
  noInfo: false,
  stats: { colors: true },
  historyApiFallback: true, // 找不到其它 html 文件时候会返回 index.html  很重要！！！
  proxy: {  // webpack-dev-serve 利用 [http-proxy-middleware](https://github.com/nodejitsu/node-http-proxy) 来做的简单的 代理功能
    '/api/*': {
      target: API_TARGET,
      secure: false,
    },
    '/media/*': {
      target: API_TARGET,
      secure: false,
    },
  }
});

server.listen(7070, '0.0.0.0', function (err, result) {
  if (err) {
    console.log(err);
  }
  console.log('Listening at localhost:7070/');
});

module.exports = server;
```


- production 部分 ( 发布时候 )

```
var path = require('path');
var webpack = require('webpack');
var config = require('./webpack.config.js');
var ExtractTextPlugin = require('extract-text-webpack-plugin');
var HtmlWebpackPlugin = require('html-webpack-plugin');

const ROOT_PATH = path.resolve(__dirname);
const src = path.resolve(ROOT_PATH, 'src');
const dist = path.resolve(ROOT_PATH, 'dist');
const build = path.resolve(ROOT_PATH, 'build');

module.exports = {
  devtool: false,

  entry: {
    //配置入口文件，有几个写几个。我这里有两个文件。一个是所有我需要引入的文件，一个是我的入口文件，index.js
    //支持数组形式，将加载数组中的所有模块，但以最后一个模块作为输出,比如下面数组里面的js,全部压缩在了vendor这个文件这里
    vendors: ['jquery'],
    app: './src/index.js',
  },

  output: {
    path: path.join(__dirname, 'build'),
    // publicPath: '/',
    publicPath: '/static/', //模板、样式、脚本、图片等资源对应的server上的路径
    filename: 'bundle.[hash:8].js'
    // chunkFilename: 'js/[id].bundle.js'   //dundle生成的配置
  },

    loaders: [{
        test: /\.js?$/,
        exclude: /node_modules/,
        include: [src, './mock'],
        loader: 'babel'
      },
      {
        test: /\.scss$/,
           // 将 css 单独打包出来
        loader: ExtractTextPlugin.extract('style-loader', 'css!postcss!sass')
      }, {
        test: /\.css$/,
        // 将 css 单独打包出来
        loader: ExtractTextPlugin.extract('style-loader', 'css'),
      }, {
        test: /\.(woff|woff2|ttf|eot|svg)\??.*$/,
        loader: 'url-loader?limit=8192&name=./fonts/[name].[ext]'
      }, {
        test: /\.(png|jpe?g|gif)$/,
        loader: 'url-loader?limit=8192&name=./imgs/[name].[ext]'
      }
    ]
  },
  // 编译 css 的时候加上 autoprefixer
  postcss: config.postcss,

  resolve: config.resolve,

  plugins: [
    // 全局的环境变量
    new webpack.DefinePlugin({
      'process.env': {
        NODE_ENV: '"production"'
      }
    }),
    new ExtractTextPlugin('./css/style.[hash:8].css'),
    new webpack.ProvidePlugin({
      $: "jquery",
      jQuery: "jquery",
      "window.jQuery": "jquery",
      "React": "react"
    }),
    // 加入 minify 压缩选项
    new HtmlWebpackPlugin({
      filename: './index.html',
      template: './src/index.html',
      title: 'Job',
      inject: true,
      hash: true,
      // chunks: ['vendor', 'app'], //需要引入的chunk，不配置就会引入所有页面的资源.名字来源于你的入口文件.
      minify: {
        removeComments: true,  // 移除HTML中的注释
        collapseWhitespace: true  // 删除空白符与换行符
      }
    }),
    // 压缩 JS，删除 console.log 之类
    new webpack.optimize.UglifyJsPlugin({
      compress: {
        warnings: false,
        screw_ie8: true,
        drop_console: true,
        drop_debugger: true
      }
    }),
    // 对比id的使用频率和分布来得出最短的id分配给使用频率高的模块
    new webpack.optimize.OccurenceOrderPlugin(),
    // 找到重复文件并去重。保证了重复的代码不被大包到bundle文件里面去，取而代之的是运行时请求一个封装的函数。
    new webpack.optimize.DedupePlugin(),
    //将引入的第三方库打包.这个要放到最后
    // vendors 对应 entry 部分，打包相应的文件
    // vendors.[hash:8].js 为打包生成的文件名
    new webpack.optimize.CommonsChunkPlugin('vendors', 'vendors.[hash:8].js'),
  ]
};

```

##### package.json

```
"scripts": {
  "lint": "eslint src",
  "cleardev": "rm -rf dist/",
  "clearb": "rm -rf build/",
  "dev": "npm run cleardev&&webpack-dev-server --config server.js --progress --profile --colors",
  "build": "npm run clearb&&webpack --config webpack.prod.config.js --progress --profile --colors --display-error-details"
},
```

{% blockquote %}

webpack 资料参照：

- [webpack-dev-serve官网](http://webpack.github.io/docs/webpack-dev-server.html)

- webpack 全套参考: [webpack 傻瓜指南](https://github.com/vikingmute/webpack-for-fools)

- 多页的参考[webpack 性能优化](https://zhuanlan.zhihu.com/p/20914387?refer=jscss)

- [Express结合Webpack的全栈自动刷新](http://acgtofe.com/posts/2016/02/full-live-reload-for-express-with-webpack)

- 参考 [webpack 性能优化](https://zhuanlan.zhihu.com/p/20914387?refer=jscss)

{% endblockquote %}
