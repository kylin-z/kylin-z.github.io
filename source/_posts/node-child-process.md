---
title: node子进程与调度任务
date: 2017-03-30 11:12:57
tags: [node,调度]
categories: node
---
## 前言

有时候我们会需要在服务端去执行一些命令或者脚本，这时候可以使用`node`的`child_process`模块来新建子进程。

## 新建子进程

`child_process`存在几种创建子进程的方法。[参考](http://nodejs.cn/api/child_process.html#child_process_child_process_spawn_command_args_options)

- 创建异步进程
  - `child_process.exec(command[, options][, callback])` 衍生一个 shell 并在 shell 上运行一个命令，当完成时会传入 stdout 和 stderr 到一个回调。
  - `child_process.execFile(file[, args][, options][, callback])`和 `child_process.exec()` 类似，但它直接衍生命令，且不用先衍生一个 shell。
  - `child_process.fork(modulePath[, args][, options])`衍生一个新的 Node.js 进程，并通过建立一个允许父进程和子进程之间相互发送信息的 IPC 通讯通道来调用一个指定的模块。
  - `child_process.spawn(command[, args][, options])`方法会异步地衍生子进程，且不会阻塞 Node.js 的事件循环。
  	- `options.detached`
  	- `options.stdio`
- 创建同步进程
  - `child_process.execFileSync(file[, args][, options])`函数则以同步的方式提供了与`child_process.execFile()`相同的功能，但会阻塞事件循环，直到衍生的子进程退出或终止。
  - `child_process.execSync(command[, options])`函数则以同步的方式提供了与`child_process.exec()`相同的功能，但会阻塞事件循环，直到衍生的子进程退出或终止。
  - `child_process.spawnSync(command[, args][, options])`函数则以同步的方式提供了与`child_process.spawn()`相同的功能，但会阻塞事件循环，直到衍生的子进程退出或终止。

这里以`spawn`方法为例。

<p class="tip">`exec`与`spawn`放法都可以满足我异步执行命令的需求，但除此之外我还需要实时获取子进程的输出，而`exec`只能在执行完之后一次获取输出，所以最终选择了`spawn`。</p>

<!-- more -->

### `spawn`方法语法

```js
child_process.spawn(command[, args][, options])
```
### `spawn`方法参数

- command <String> 要运行的命令
- args <Array> 字符串参数列表

### 例子

```js
const child_process = require("child_process");

const spawn = child_process.spawn;

const child = spawn('node',['index.js','html','42']);

// node index.js html 42
```

## 获取子进程输出

```js
//标准输出流
child.stdout.pipe(es.split('\n')).pipe(es.through((data) => {
  log.write(`Stdout | ${data}`)
}));
```
<p class="tip">原本使用的是`child.stdout.on('data',callback)`方法，但此方法在同一时间输出数据较多的情况下会一瞬间输出获取到这些输出数据，这里我想要一行一行的将输出写入到`log`日志中，这时可以使用`pipe`方法，现将整段数据分开，再进行写入。</p>

## 捕获错误

```js
//错误
child.stderr.on('data', (data) => {
  log.write(`Stderr | ${data}`)
});
```

## 退出

### 参数
- code <Number> 如果子进程退出自身，则该值是退出码。
- signal <String> 子进程被终止时的信号。

子进程结束后会触发 'exit' 事件。 如果进程退出了，则 code 是进程的最终退出码，否则为 null。 如果进程是收到的信号而终止的，则 signal 是信号的字符串名称，否则为 null。 这两个总有一个是非空的。

<p class="tip">当 'exit' 事件被触发时，子进程的 stdio 流可能依然是打开的。</p>

```js
child.on('exit', (code,signal) => {
  console.log(`子进程退出码：${code}`);
});
```

## 完整案例

```js
const spawn = require('child_process').spawn;
const ls = spawn('ls', ['-lh', '/usr']);

ls.stdout.on('data', (data) => {
  console.log(`stdout: ${data}`);
});

ls.stderr.on('data', (data) => {
  console.log(`stderr: ${data}`);
});

ls.on('close', (code) => {
  console.log(`子进程退出码：${code}`);
});
```

## 获取命令行参数

```bash
node  test.js arg1 arg2 arg3
# node test.js 24 52
```
`process`是一个全局对象，`argv`返回的是一组包含命令行参数的数组，splice(index)删除包括下标index在内的所有后续元素并返回删除元素组成的数组

```js
console.log(process.argv)
// ['/usr/local/bin/node','/Users/kylinzhou/Documents/Himmas/himmas_super_admin/spiders/test.js','24','52' ]

const params = process.argv.splice(2);
// ['24','52']

const arg1 = params[0];
const arg2 = params[1];
```
