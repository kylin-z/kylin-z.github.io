---
title: python学习笔记（一）
date: 2017-02-17 13:38:17
tags: [python]
categories: python
---
## 编码问题

<p class="tip">在编写Python时，当使用中文输出或注释时运行脚本，会提示错误信息：</p>

![](http://7xrqm7.com1.z0.glb.clouddn.com/python-encoding-error.png)

### 原因:
python的默认编码文件是用的ASCII码,而编辑器讲文件存成了UTF-8

### 解决方案:
在文件第一行添加以下代码
```python
# -*- coding: UTF-8 -*-    
```
或者  

```python
#coding=utf-8
```

## 字符串

```python
print r'\\\t\\\ '
# r''表示''内部字符默认不转义

# 如果字符串内部有很多换行，用\n写在一行里不好阅读，为了简化，Python允许在交互式命令行中用'''...'''
# 如 >>> print '''line1
# ... line2
# ... line3'''
# 的格式表示多行内容,若写成程序则如下:

print '''line1
line2
line3'''

# r''还能与'''...'''结合如下:

print r'''\nline1
line2
line3'''
```
<!-- more -->

## 整型与浮点数
<p class="tip">在python中，两个整数的除法永远是精确的，因为整数除法只取结果的整数部分。</p>

```python
>>> 10 / 3
3
# js中10/3=3.3333333333333335
```
要做精确的除法，只需把其中一个整数换成浮点数做除法就可以：

```python
>>> 10.0 / 3
3.3333333333333335
```

## 格式化

常见的占位符有：

- %d	整数
- %f	浮点数
- %s	字符串
- %x	十六进制整数

占位符可以用来实现类似字符串替换的功能，如

```python
>>> 'Hello, %s' % 'world'
'Hello, world'
>>> 'Hi, %s, you have $%d.' % ('Michael', 1000000)
'Hi, Michael, you have $1000000.'
```
<p class="tip">有几个%?占位符，后面就跟几个变量或者值，顺序要对应好。如果只有一个%?，括号可以省略。</p>

```python
a = '%2d-%02d-%03d'%(3,1,1)
# 补位 ' 3-01-001'
print a

a = '%.2f' % 3.1415926
# 保留两位小数 '3.14'
print a

a = '%09.2f' % 3.1415926
# '000003.14' 全长为9 占位符为0 保留两位小数 不写0则占位符为 ' '
print a
```

## list
### `len()`获取list的长度,`classmates[-1]`使用-1索引可以获取最后一个元素,`classmates[-2]`-2获取倒数第二个，依次类推
```python
classmates = ['Michael','Bob','Tracy']
print classmates,len(classmates),classmates[-1]
# ['Michael', 'Bob', 'Tracy'] 3 Tracy
```

### `append()`追加元素到list末尾

```python
>>> classmates.append('Adam')
>>> classmates
['Michael', 'Bob', 'Tracy', 'Adam']
```

### `pop()` 返回list末尾的元素，并将其从list中删除

```python
>>> classmates.pop()
'Adam'
>>> classmates
['Michael', 'Jack', 'Bob', 'Tracy']

# pop(i) 取&删除制定位置
>>> classmates.pop(1)
'Jack'
>>> classmates
['Michael', 'Bob', 'Tracy']
```

### `insert()`插入元素到指定位置

```python
>>> classmates.insert(1, 'Jack')
>>> classmates
['Michael', 'Jack', 'Bob', 'Tracy', 'Adam']
```

## tuple

`tuple` 与 `list` 非常相似，但是tuple初始化后不可修改

```python
>>> classmates = ('Michael', 'Bob', 'Tracy')

>>> classmates[0] = 1
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
# 强行修改会报错
```
### 定义只有一个元素的`tuple`

```python
>>> t = (1)
>>> t
1
# 这样定义定义的不是tuple，是1这个数
```
<p class="tip">定义只有一个元素的`tuple`时，必须加一个`,`</p>

```python
>>> t = (1,)
>>> t
(1,)
```


### '可变的'tuple

<p class="tip">`tuple`的“不变”是指tuple的每个元素，指向永远不变。</p>

如以下操作是可以的

```python
>>> t = ('a', 'b', ['A', 'B'])
>>> t[2][0] = 'X'
>>> t[2][1] = 'Y'
>>> t
('a', 'b', ['X', 'Y'])
```
