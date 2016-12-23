---
title: babel & eslint 配置
categories:
- 编程
tags:
- 记录
- 存档
- 环境
---

存档使用
 
 <!-- more -->

- .babelrc

```
// in .babelrc
{
  "presets": ["react", "es2015", "stage-0"],
}
// 好像要 install 这几个
// babel-plugin-transform-object-rest-spread
// babel-preset-es2015
// babel-preset-react
// babel-preset-react-hmre
//babel-preset-stage-0
```

- .eslintrc

```
// in .eslintrc
{
    "parser": "babel-eslint",
    "extends": "airbnb",   
    // 好像要额外 isntall 这几个
    // eslint-config-airbnb
    // eslint-config-rackt
    // eslint-plugin-import
    // eslint-plugin-jsx-a11y

    "env": {
      "browser": true,
      "commonjs": true,
      "es6": true,
      "jquery": true
    },
    "rules": {
      // "max-len": [1, 120, 2],
      // "max-len": [1, 120, 2, {ignoreComments: true}],
      "arrow-parens": 0,
      "react/prop-types": [2, {ignore: ["children"]}],
      "react/jsx-filename-extension": [1, { "extensions": [".js", ".jsx"] }],
      "react/jsx-no-undef": 2,
      "no-console": 0,
    }
}
```
