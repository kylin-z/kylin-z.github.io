---
title: python学习笔记
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
