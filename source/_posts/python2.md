---
title: python学习笔记（二）
date: 2017-02-20 11:15:09
tags: [python]
categories: [python,笔记]
---

## range()函数

`range(n)`函数可以生成一个0~n-1整数序列

```python
>>> range(5)
[0, 1, 2, 3, 4]
```
## 类型转换

### string 转 int——`int()`

```python
birth = int(raw_input('birth: '))
```

## dict

`dict`与js中的`Object`相似，不同的是不能像js一样用`.`运算符读取

```python
>>> d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
>>> d['Michael']
95
```
### 判断key是否存在

- `in`
```python
>>> 'Thomas' in d
False
```

- `get()`

第二个参数可以指定不存在时返回的值

```python
>>> d.get('Thomas')
>>> d.get('Thomas', -1)
-1
```

### 删除并返回指定元素

```python
>>> d.pop('Bob')
75
>>> d
{'Michael': 95, 'Tracy': 85}
```

<!-- more -->

## set

> set和dict类似，也是一组key的集合，但不存储value。由于key不能重复，所以，在set中，没有重复的key。
```python
>>> s = set([1, 2, 3])
>>> s
set([1, 2, 3])
```

### `add(key)`添加元素

```python
>>> s.add(4)
>>> s
set([1, 2, 3, 4])
>>> s.add(4)
>>> s
set([1, 2, 3, 4])
```

### `remove(key)`删除元素

```python
>>> s.remove(4)
>>> s
set([1, 2, 3])
```

### 交集与并集

```python
>>> s1 = set([1, 2, 3])
>>> s2 = set([2, 3, 4])
>>> s1 & s2
set([2, 3])
>>> s1 | s2
set([1, 2, 3, 4])
```

## 函数

### 空函数占位符

```python
def nop():
    pass
```

<p class="tip">`pass`还可以用在其他的语句里，比如`if`等。</p>
