---
title: 关于js异步
date: 2017-01-04 09:58:40
tags: [javascript,前端,回调]
categories: [笔记]
---

## 同步Ajax

<p class="tip">在任何情况下都不应该使用这种方式,因为它会锁定浏览器UI(按钮、菜单、滚动条等),并阻塞所有的用户交互。 </p>

## error-first风格的超时报错处理

```js
function timeoutify(fn,delay){
  var intv = setTimeout( function(){
    intv = null
    fn(new Error("Timeout"))
  },delay)

  return function(){
    if(intv){
      clearTimeout(intv)
      fn.apply(this,arguments)
    }
  }
}
```
<!-- more -->

### 使用方式 [gist](https://gist.githubusercontent.com/ruolinxue/0f2f4722bf997d269a37b5a2618be651/raw/35bb3528b3f0edb1f3884b68b57b9f06ff649184/timeoutify-example.js)

```js
function foo(err,data){
  if(err)
    console.log(err)
  else
    console.log(data)
}

function timeoutify(fn,delay){

  var intv = setTimeout( function(){
		// ----A段代码----
    intv = null
    fn(new Error("Timeout"))
		// ----A段代码----
  },delay)

  return function(){
    if(intv){
      clearTimeout(intv)
      fn.apply(this,arguments)
    }
  }
}

function bar(fn){
  setTimeout(function(){
    fn('no timeout')
  },500)
}

bar(timeoutify(foo,100))
//Error: Timeout
//    at <anonymous>:1:1

bar(timeoutify(foo,600))
//no timeout
```
<p class="tip">timeoutify方法返回一个function 若在delay时间前调用，则会清空计时器，若delay时间先到则会先运行A段代码，讲intv变量设为null并抛出错误</p>

## Promise

```js
function timeout(ms){
  return new Promise((resolve,reject)=>{
    setTimeout(resolve,ms,'done')//IE9以下不支持传递额外参数
  })
}
timeout(100).then((value)=>{
  console.log(value)
})
```
