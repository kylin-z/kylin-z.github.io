---
title: vue插件的打包与发布
date: 2018-02-08 14:35:04
tags: [vue,webpack]
categories: vue
---

## 初始化项目

以我刚发布的[`vue-calendar`](https://github.com/kylin-z/vue-calendar)组件为例

开发插件用`webpack-simple` 模板比较合适

```bash
vue init webpack-simple vue-calendar
cd vue-calendar
npm install
```

## 在跟目录下新建`test`文件夹用于开发测试

我这里将原本位于`src`文件夹下的`main.js`以及`App.vue`文件转移到了`test`文件夹下

![](http://7xrqm7.com1.z0.glb.clouddn.com/vue-plugin-1.png)

## 将插件源码放在`src`文件夹下，插件入口为`src/calendar/main.js`

![](http://7xrqm7.com1.z0.glb.clouddn.com/vue-plugin-2.png)

## 修改 `webpack.config.js` 文件

根据不同的命令(环境变量)设置不同的入口与输出文件，保证`npm run dev`与`npm run build`两个命令都为可用

```js
var path = require('path')
var webpack = require('webpack')

//...根据环境变量(package.json script中设置)...
var entry = './test/main.js'
var outputFileName = 'build.js'

if (process.env.NODE_ENV === 'production') {
  entry = './src/calendar/main.js'
  outputFileName = 'vue-calendar.js'
}
//......

module.exports = {
  //entry改为变量
  entry: entry,
  output: {
    path: path.resolve(__dirname, './dist'),
    publicPath: '/dist/',
    //filename
    filename: outputFileName,
    //library与libraryTarget一定要添加，否则import进来的对象为空
    library: 'vue-calendar',
    libraryTarget: 'umd'
  },
  //...
}
```
<!-- more -->
<p class="tip">这里要注意非常重要的一点，webpack默认你构建的内容是一个应用而非library，如果不设置`library`将会导致你在使用该插件的时候`import`进来的对象为空。</p>

> A webpack bundle does not expose your exports by default, as it assumes that you're building an app and not a library (which is the far more common use of webpack). You can create a library by configuring output.library and output.libraryTarget [参考一](https://stackoverflow.com/questions/44541561/webpack-output-is-empty-object) [参考二](https://webpack.js.org/configuration/output/#output-library)

## 修改package.json

使用`npm run build`或其他方式打包。

- 将`private`设置为`false`
- 将`main`设置为打包产出的文件路径

```json
{
  "name": "himmas-vue-calendar",
  "description": "A calendar component for Vue.js",
  "version": "1.1.2",
  "author": "kylin-z <kailinzhou@yeah.net>",
  "license": "MIT",
  "scripts": {
    "dev": "cross-env NODE_ENV=development webpack-dev-server --open --hot",
    "build": "cross-env NODE_ENV=production webpack --progress --hide-modules"
  },
  "private": false,
  "main": "dist/vue-calendar.js",
  "repository": {
    "type": "git",
    "url": "https://github.com/kylin-z/vue-calendar.git"
  },
  "browserslist": [
    "> 1%",
    "last 2 versions",
    "not ie <= 8"
  ],
  "keywords": [
    "calendar",
    "custom",
    "vue"
  ]
  //...
}
```

## 发布到npm

- 在控制台登录`npm`账号
  ```bash
  $ npm login
  ```
- 发布(每次发布需要更新`version`)
  ```bash
  $ npm publish
  ```

