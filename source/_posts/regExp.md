---
title: RegExp对象踩坑记录
date: 2017-04-25 14:45:01
tags: [RegExp,javascript]
categories: javascript
---

## `g` 与 `test()`

### 现象

带`g`的正则对象多次调用`test()`结果不同

```js
var reg = new RegExp('51-(.*)','g')
reg.test('51-in')
// true
reg.test('51-in')
// false
```
### 原因

> _每个RegExp对象都包含5个属性。属性source是一个只读的字符串，包含正则表达式的文本。属性global是一个只读的布尔值，用以说明这个正则表达式是否带有修饰符g。属性ignoreCase也是一个只读的布尔值，用以说明正则表达式是否带有修饰符i，属性multiline是一个只读布尔值，用以说明正则表达式是都带有修饰符m。最后一个属性lastIndex，它是一个可读/写的整数。如果匹配模式带有g修饰符，这个属性存储在整个字符串中下一次检索的开始位置，这个属性会被exec()和test()方法用到。_

```js
var reg = new RegExp('51-(.*)','g')
reg.test('51-in')
// true
reg.lastIndex
// 5
reg.test('51-in')
// false
reg.lastIndex
// 0
```
### 解决姿势

> _与exec()和test()不同，String方法search()、replace()、match()并不会用到lastIndex属性。实际上，String方法只是简单地将lastIndex属性值重置为0。如果让一个带有修饰符g的正则表达式对多个字符串执行exec()或test()，要么在每个字符串中找出所有的匹配以便将lastIndex自动重置为0，要么显式将lastIndex手动设置为0（当最后一次检索失败时需要手动设置lastIndex）。如果忘了手动设置lastIndex的值，那么下一次对新字符串进行检索时，执行检索的起始位置可能就不是字符串的开始位置，而可能是任意位置。当然，如果RegExp不带修饰符g，则不必担心会发生这种情况。同样要记住，在ECMAScript5中，正则表达式直接量每次计算都会创建一个新的RegExp对象，每个新RegExp对象具有各自的lastIndex属性，这势必会大大减少“残留”lastIndex对程序造成的意外影响。_

根据以上说明，可是采用一下几种替换方式

- `test()`方法不使用`g`
- 使用正则表达式直接量
- 每次都重新`new RegExp`
- 手动重置`lastIndex`
