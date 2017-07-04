---
title: 扩展运算符小贴士
date: 2017-07-04 13:45:59
tags: [es6,javascript,codewar]
categories: javascript
---
## 前沿

es2015引入了扩展运算符(spread operator)[`...`](http://es6.ruanyifeng.com/#docs/array)，可以用来进行一些数组和对象操作。

## 案例

下面是codewar上的一道题，判断字符串/数字/数组是否对称

### 题目

```bash

Give you an obj, it can be 3 types: string, number and number array, Check that they are symmetrical or not, return a boolean value.  

obj=""    return true   (Empty string should return true)
obj="1"   return true   (one char should return true)
obj="11"  return true
obj="12"  return false
obj="121" return true
obj=1     return true   (number<10  should return true)
obj=-1    return false  (negative number should return false)
obj=10    return false
obj=11    return true
obj=12    return false
obj=121   return true
obj=[]    return true  (Empty array should return true)
obj=[1]   return true  (an array with one element should return true)
obj=[1,2,3,4,5]      return false  
obj=[1,2,3,2,1]      return true
obj=[11,12,13,12,11] return true   (left element = right element)
obj=[11,12,21,11]    return false  (not verify them as a string)
```

<!-- more -->

### 分析

考虑到[11,12,13,12,11]也被定义为对称，所以应该将字符串，数字等都转换为数组，而不是将数组转换为字符串。
转换为数组后通过判断数组与数组的倒序数组是否相等来得出最终的结果(obj.join('')==obj.reverse().join(''))

### 解法

```js
function sc(o){
  o = Array.isArray(o)?o:String(o).split('')
  return o.join('') == o.reverse().join('')
}
```

根据分析可以得出以上解法，但题目本身还存在代码字数上的限制(85个以内),以上解法显然长度超标。

在数组、数组以及字符串转换阶段做文章，可以得到以下这个比较简短的方法。

```js
sc=o=>{
  s=[...(isNaN(o)?o:`${o}`)]
  return `${s}`==`${s.reverse()}`
}
```

让我们对他进行分析

- 判断是否是数字，若是则加上引号转换为字符串
- 使用扩展运算符转换为数组
- 讲数组转为字符串判断是否与反序数组相等

<p class="tip">`...`运算符可以将一个数组直接在另一个数组中展开</p>

```js
[...[1,2,3]]
//[1,2,3]
```

<p class="tip">`...`运算符讲类数组对象(带有属性length)转换为数组对象然后再另一个数组中展开</p>

```js
[..."123"]
//[1,2,3]
```

综上所述只要将数字类型转换成字符串就可以将以上三种类型统一转换成数组。


## 关于字符串与数字相互转换

<p class="tip">数字转换成数组可以`n.toString()` 、`new String(n)`等方法，也可以使用es6的字符串模板或者更简单的n+'' 通过加一个字符串来进行转换，由于题目的字数限制，这里选择的后面两种比较简短的方式</p>


```js
123 + ''
//"123"
`${123}`
//"123"
```

<p class="tip">字符串转换成数字除了可以使用常规的`parseInt(n)`、`Number()`等方法之外，还可以使用两个`~`操作符，值得一提的是，`~~`不仅可以将正常的数字字符串转换成number，还可以将假值转换为0</p>

```js
~~'1'
//1
~~undefined
//0
~~null
//0
~~''
//0
~~false
//0
~~+0
//0
~~-0
//0
~~NaN
//0
```
