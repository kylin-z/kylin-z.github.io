---
title: snabbdom源码阅读笔记（二）
date: 2016-12-27 14:43:01
tags: ['VirtualDom','javascript','前端']
categories: 笔记
---
## hook

| name | 调用时间 | 参数 |
|---  |---|---|
| pre | patch 开始的时候 | 无 |
| init | vnode 被添加时 | vnode |
| create|	a DOM element has been created based on a vnode	emptyVnode |  vnode|
|insert	|an element has been inserted into the DOM	|vnode|
| prepatch |	patch 开始之前	|oldVnode, vnode|
|update|	an element is being updated	|oldVnode, vnode|
|postpatch|	an element has been patched	|oldVnode, vnode|
|destroy|	an element is directly or indirectly being removed	|vnode|
|remove|	an element is directly being removed from the DOM	|vnode, removeCallback|
|post|	patch结束之后	| 无 |
