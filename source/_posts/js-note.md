---
title: js随笔
date: 
tags: js
categories: 笔记
---

## 函数声明和函数表达式
函数表达式的name不能在函数外部被使用
iife是一个函数表达式
```js
(function foo(){})
foo()
//会报错
```
[参考](https://www.zhihu.com/question/60063924)
[参考](http://www.cnblogs.com/TomXu/archive/2012/01/30/2326372.html)

## 变量对象(VO)

旦进入执行上下文（在执行代码之前），VO就会被一些属性填充：

- 函数的形参（当进入函数执行上下文时）
  变量对象的一个属性，其属性名就是形参的名字，其值就是实参的值；对于没有传递的参数，其值为undefined
- 函数声明（FunctionDeclaration, FD） —— 变量对象的一个属性，其属性名和值都是函数对象创建出来的；如果变量对象已经包含了相同名字的属性，则替换它的值
- 变量声明（var，VariableDeclaration） —— 变量对象的一个属性，其属性名即为变量名，其值为undefined;如果变量名和已经声明的函数名或者函数的参数名相同，则不会影响已经存在的属性。

<!-- more -->
```js
function test(a, b) {
  var c = 10;
  function d() {}
  var e = function _e() {};
  (function x() {});
}
 
test(10); // call
// 当以10为参数进入“test”函数上下文的时候，对应的AO(VO)如下所示：
//函数上下文中 VO === AO

AO(test) = {
  a: 10,
  b: undefined,
  c: undefined,
  d: 
  e: undefined
};
```



```js
alert(x); // function
 
var x = 10;
alert(x); // 10
 
x = 20;
 
function x() {};
 
alert(x); // 20
```

```js
var a = 10
b = 20
delete window.a
delete window.b

```