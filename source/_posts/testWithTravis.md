---
title: TravisCI自动化构建与Karma测试实践记录
date: 2017-01-23 17:24:29
tags: ['测试','karma','travisCI','自动化构建']
categories: ['自动化']
---
## Karma

### 安装的依赖

```
$ npm install karma --save-dev
$ npm install karma-jasmine karma-chrome-launcher jasmine-core --save-dev
```
装完依赖后可以用以下命令运行测试

```
# Run Karma:
$ ./node_modules/karma/bin/karma start
```
若觉得`./node_modules/karma/bin/karma`的写法太长，则可以全局安装`karma-cli`

```
npm install -g karma-cli
```

### 初始化配置

最简单的方法是使用命令`karma init`来创建配置文件

```bash

$ karma init

Which testing framework do you want to use ?

Press tab to list possible options. Enter to move to the next question.
> jasmine


Do you want to use Require.js ?
This will add Require.js plugin.
Press tab to list possible options. Enter to move to the next question.
> no

Do you want to capture any browsers automatically ?
Press tab to list possible options. Enter empty string to move to the next question.
> Chrome
>

What is the location of your source and test files ?
You can use glob patterns, eg. "js/*.js" or "test/**/*Spec.js".
Enter empty string to move to the next question.
>

Should any of the files included by the previous patterns be excluded ?
You can use glob patterns, eg. "**/*.swp".
Enter empty string to move to the next question.
>

Do you want Karma to watch all the files and run the tests on change ?
Press tab to list possible options.
> yes

Config file generated at "/Users/xx/Documents/Himmas/melody/karma.conf.js".

```

<!-- more -->

生成配置文件如下:

```js
// Karma configuration
// Generated on Mon Jan 23 2017 17:49:35 GMT+0800 (CST)

module.exports = function(config) {
  config.set({

    // base path that will be used to resolve all patterns (eg. files, exclude)
    basePath: '',


    // frameworks to use
    // available frameworks: https://npmjs.org/browse/keyword/karma-adapter
    frameworks: ['jasmine'],


    // list of files / patterns to load in the browser
    files: [
    ],


    // list of files to exclude
    exclude: [
    ],


    // preprocess matching files before serving them to the browser
    // available preprocessors: https://npmjs.org/browse/keyword/karma-preprocessor
    preprocessors: {
    },


    // test results reporter to use
    // possible values: 'dots', 'progress'
    // available reporters: https://npmjs.org/browse/keyword/karma-reporter
    reporters: ['progress'],


    // web server port
    port: 9876,


    // enable / disable colors in the output (reporters and logs)
    colors: true,


    // level of logging
    // possible values: config.LOG_DISABLE || config.LOG_ERROR || config.LOG_WARN || config.LOG_INFO || config.LOG_DEBUG
    logLevel: config.LOG_INFO,


    // enable / disable watching file and executing tests whenever any file changes
    autoWatch: true,


    // start these browsers
    // available browser launchers: https://npmjs.org/browse/keyword/karma-launcher
    browsers: ['Chrome'],


    // Continuous Integration mode
    // if true, Karma captures browsers, runs the tests and exits
    singleRun: false,

    // Concurrency level
    // how many browser should be started simultaneous
    concurrency: Infinity
  })
}

```

这里修改几个地方

```js
// Karma configuration
// Generated on Mon Jan 23 2017 10:53:08 GMT+0800 (CST)


module.exports = function(config) {
    var configuration = {
        // other things
        //根目录，files的路径从这里开始取
        // base path that will be used to resolve all patterns (eg. files, exclude)
        basePath: '../../',


        // frameworks to use
        // available frameworks: https://npmjs.org/browse/keyword/karma-adapter
        frameworks: ['jasmine'],

        // 源文件与测试用例
        // list of files / patterns to load in the browser
        files: [
            'src/modules/**/*.js',
            'test/unit/specs/**/*.spec.js'
        ],


        // list of files to exclude
        exclude: [],


        // preprocess matching files before serving them to the browser
        // available preprocessors: https://npmjs.org/browse/keyword/karma-preprocessor

        // es6代码做预处理
        preprocessors: {
            'src/modules/**/*.js': ['babel', 'coverage', 'sourcemap'],
            'test/unit/specs/**/*.spec.js': ['babel', 'coverage', 'sourcemap']
        },


        // test results reporter to use
        // possible values: 'dots', 'progress'
        // available reporters: https://npmjs.org/browse/keyword/karma-reporter
        reporters: ['progress', 'kjhtml', 'coverage'], //kjhtml 生成html测试报告

        // web server port
        port: 9876,


        // enable / disable colors in the output (reporters and logs)
        colors: true,


        // level of logging
        // possible values: config.LOG_DISABLE || config.LOG_ERROR || config.LOG_WARN || config.LOG_INFO || config.LOG_DEBUG
        logLevel: config.LOG_INFO,


        // enable / disable watching file and executing tests whenever any file changes
        autoWatch: true,


        // start these browsers
        // available browser launchers: https://npmjs.org/browse/keyword/karma-launcher
        browsers: ['Chrome'],


        // Continuous Integration mode
        // if true, Karma captures browsers, runs the tests and exits
        singleRun: false,

        // Concurrency level
        // how many browser should be started simultaneous
        concurrency: Infinity,

        // proxies 资源文件代理
        proxies: {
            "/media/": "http://localhost:4002/media/"
        },

        // coverage output setting
				// 覆盖率报告配置
        coverageReporter: {
            instrumenterOptions: {
                istanbul: {
                    noCompact: true
                }
            },
            reporters: [{
                type: 'html',
                dir: './test/unit/coverage/' //报告文件夹
            }, {
                type: 'text-summary'
            }],
        }
    };

    config.set(configuration)
}

```
### 新建待测试文件

```js
// src/modules/test.js
export const plus = function (a,b) {
	return a + b
}
```

### 新建测试用例

```js
// test/unit/specs/test.spec.js
import { plus } from '/src/module/test'

describe("This is a plus test!", function() {

    it("is an addtion function", function() {

        expect(20).toEqual(plus(15,5));

    })

});
```

 Suite表示一个测试集，以函数`describe(string, function)`封装，它包含2个参数：

- `string`：测试组名称，
- `function`：测试组函数。
- 一个Suite(`describe`)包含多个Specs(`it`)，一个Specs(`it`)包含多个断言（`expect`）。

<p class="tip">这里使用的是单元测试框架<a href="https://jasmine.github.io/">Jasmine</a>,你可以使用你喜欢的测试框架如`mocha`等，并在karma.config.js的`frameworks`选项中进行配置。</p>

### 运行测试

```
$ karma start test/unit/karma.conf.js
```

也可以将命令写在`package.json`文件中，然后运行命令`npm run test`

```js
{
  //...省略
  "scripts": {
    "test": "karma start test/unit/karma.conf.js"
  }//...省略
}
```

测试结果如下

```bash
kylindeMacBook-Pro:melody kylin$ npm run test

> melody@1.0.0 test /Users/kylin/Documents/Himmas/melody
> karma start test/unit/karma.conf.js

24 01 2017 09:17:16.001:WARN [karma]: No captured browser, open http://localhost:9876/
24 01 2017 09:17:16.015:WARN [karma]: Port 9876 in use
24 01 2017 09:17:16.018:INFO [karma]: Karma v1.4.0 server started at http://0.0.0.0:9877/
24 01 2017 09:17:16.020:INFO [launcher]: Launching browser Chrome with unlimited concurrency
24 01 2017 09:17:16.059:INFO [launcher]: Starting browser Chrome
24 01 2017 09:17:18.229:INFO [Chrome 51.0.2704 (Mac OS X 10.11.4)]: Connected on socket sPWeedIJ8vBO0P1RAAAA with id 84748433
Chrome 51.0.2704 (Mac OS X 10.11.4): Executed 1 of 1 SUCCESS (0 secs / 0.006 secsChrome 51.0.2704 (Mac OS X 10.11.4): Executed 1 of 1 SUCCESS (0.285 secs / 0.006 secs)

=============================== Coverage summary ===============================
Statements   : 47.9% ( 57/119 )
Branches     : 27.08% ( 13/48 )
Functions    : 56% ( 14/25 )
Lines        : 49.14% ( 57/116 )
================================================================================

```

chrome51浏览器会自动打开,界面如下

![](http://7xrqm7.com1.z0.glb.clouddn.com/karma9876.png)

由于在配置文件中设置了html格式的测试报告，点击页面右上角debug可以看到

![](http://7xrqm7.com1.z0.glb.clouddn.com/karma-debug.png)


## TravisCI

有些项目(静态博客等)，我们希望每次push完，测试通过之后，能自动build并将生成的文件push到gh-pages分支中去。`Travis CI`就能很好地完成这个需求。

![](http://7xrqm7.com1.z0.glb.clouddn.com/travisCI.png)

### 需求
以[medoly](https://github.com/Himmas/melody)这个项目为例，它的目录结构如下


![](http://7xrqm7.com1.z0.glb.clouddn.com/medoly-dir.png)


每次提交代码之后，我希望travis做两件事情

- 运行测试
- 若测试通过，将src目录push到gh-pages中去

## 如何配置

### 启用需要构建的项目

- 打开[https://travis-ci.org/](https://travis-ci.org/)，点击右上角的` Sign in with GitHub `，使用github账号登录。

![](http://7xrqm7.com1.z0.glb.clouddn.com/travisCI-login.png)

- 你可能并不会有红框区域的内容，点击左上角绿框的`+`号

![](http://7xrqm7.com1.z0.glb.clouddn.com/travisCI-add.png)

- 点击右上角的`Sync account`,讲github上的仓库同步过来，然后选择需要构建的仓库，讲前面的按钮选成开启状态

![](http://7xrqm7.com1.z0.glb.clouddn.com/travisCI-on.png)

- 去github上生成一个token：
	- 1.打开setting
	- 2.选择personal account token 选项卡
	- 3.点击右上角generate new token

![](http://7xrqm7.com1.z0.glb.clouddn.com/github-token.png)

- 输入密码之后到了一个权限勾选界面

![](http://7xrqm7.com1.z0.glb.clouddn.com/github-new-token.png)

- 点击生成，然后`将其复制下来`（刷新之后就不显示了）
- 添加travisCI 对应项目的setting 中

![](http://7xrqm7.com1.z0.glb.clouddn.com/travisCI-setting.png)

- 完成后在项目仓库中添加一个`.travis.yml`文件

```yml
language: node_js
node_js: stable

# S: Build Lifecycle
# install:
#   - npm install


#before_script:
 # - npm install -g gulp

before_install:
 - export CHROME_BIN=chromium-browser
 - export DISPLAY=:99.0
 - sh -e /etc/init.d/xvfb start

# script:
#   - hexo g

# 构建之后推送到gh-pages
after_script:
  - cd ./src
  - git init
  - git config user.name "kylin"
  - git config user.email "kailinzhou@yeah.net"
  - git add .
  - git commit -m "TravisCI"
  - git push --force --quiet "https://${GH_TOKEN}@${GH_REF}" master:gh-pages
# E: Build LifeCycle

# 设置只有master的push才进行构建
branches:
  only:
    - master
env:
 global:
   - GH_REF: github.com/Himmas/melody.git
```
> 具体配置可参考[官网](https://docs.travis-ci.com/user/getting-started)

## 踩到的坑

<p class="tip">直接在travis中使用之前的karma配置会报一下的错误</p>

![](http://7xrqm7.com1.z0.glb.clouddn.com/travis-karma-error.png)

需要对travis环境进行特殊处理，最终配置文件如下:

```js
// Karma configuration
// Generated on Mon Jan 23 2017 10:53:08 GMT+0800 (CST)


module.exports = function(config) {
    var configuration = {
        // other things
        //根目录，files的路径从这里开始取
        // base path that will be used to resolve all patterns (eg. files, exclude)
        basePath: '../../',


        // frameworks to use
        // available frameworks: https://npmjs.org/browse/keyword/karma-adapter
        frameworks: ['jasmine'],

        // 源文件与测试用例
        // list of files / patterns to load in the browser
        files: [
            'src/modules/**/*.js',
            'test/unit/specs/**/*.spec.js'
        ],


        // list of files to exclude
        exclude: [],


        // preprocess matching files before serving them to the browser
        // available preprocessors: https://npmjs.org/browse/keyword/karma-preprocessor

        // es6代码做预处理
        preprocessors: {
            'src/modules/**/*.js': ['babel', 'coverage', 'sourcemap'],
            'test/unit/specs/**/*.spec.js': ['babel', 'coverage', 'sourcemap']
        },


        // test results reporter to use
        // possible values: 'dots', 'progress'
        // available reporters: https://npmjs.org/browse/keyword/karma-reporter
        reporters: ['progress', 'kjhtml', 'coverage'],

        // web server port
        port: 9876,


        // enable / disable colors in the output (reporters and logs)
        colors: true,


        // level of logging
        // possible values: config.LOG_DISABLE || config.LOG_ERROR || config.LOG_WARN || config.LOG_INFO || config.LOG_DEBUG
        logLevel: config.LOG_INFO,


        // enable / disable watching file and executing tests whenever any file changes
        autoWatch: true,


        // start these browsers
        // available browser launchers: https://npmjs.org/browse/keyword/karma-launcher
        browsers: ['Chrome'],


        // Continuous Integration mode
        // if true, Karma captures browsers, runs the tests and exits
        singleRun: false,

        // Concurrency level
        // how many browser should be started simultaneous
        concurrency: Infinity,

        // proxies 资源文件代理
        proxies: {
            "/media/": "http://localhost:4002/media/"
        },

        // coverage output setting
        coverageReporter: {
            instrumenterOptions: {
                istanbul: {
                    noCompact: true
                }
            },
            reporters: [{
                type: 'html',
                dir: './test/unit/coverage/'
            }, {
                type: 'text-summary'
            }],
        },
        sourcemap: {
            transform: function(mapData) {
                mapData.sourceRoot = '/';
            }
        },

        customLaunchers: {
            Chrome_travis_ci: {
                base: 'Chrome',
                flags: ['--no-sandbox']
            }
        }
    };

    if (process.env.TRAVIS) {
        configuration.browsers = ['Chrome_travis_ci'];
        configuration.singleRun = true;
        // travis 环境下执行一次
    }

    config.set(configuration)
}

```

<p class="tip">在travis环境中不要忘记将singleRun设为true，否则会一直监听文件变化，不能结束test</p>
