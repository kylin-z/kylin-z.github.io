---
title: snabbdom源码阅读笔记
date: 2016-12-21 13:56:16
tags: ['Virtual Dom','javascript','前端']
categories: [笔记]
---
## vnode结构

> vnode结构定义在vnode.js中

```js
module.exports = function(sel, data, children, text, elm) {
  var key = data === undefined ? undefined : data.key;
  return {sel: sel, data: data, children: children,
          text: text, elm: elm, key: key};
};
```

### vnode属性

- sel: string | undefined
- data: (VNodeData | undefined) virtual dom包含的数据
- children: (Array<VNode | string> | undefined) virtual dom 的子节点 可以使vnode或者字符串
- elm: (Node | undefined) 对dom element的引用
- text: (string | undefined)元素的文本内容
- key: Key 用于提示 children patch 过程



## h函数

> h函数定义在h.js中

- 对存在data和不存在data的情况进行了分开处理
- 并将子元素中的 `primitive` 类型包装成vnode (`is.array` 与 `is.primitive` 定义在 `is.js` 。`primitive` 指 参数是否是`String`或`Number`类型)

```js
module.exports = function h(sel, b, c) {
  var data = {}, children, text, i;
  // 若c存在（存在第三个参数）
  if (c !== undefined) {
    data = b;
    // 若c是数组
    if (is.array(c)) { children = c; }
    // c是String或者Number,则直接赋值
    else if (is.primitive(c)) { text = c; }
  } else if (b !== undefined) { //若存在data
    //如果b是数组（b参数为子元素数组）
    if (is.array(b)) { children = b; }
    // 如果b是String或Number
    else if (is.primitive(b)) { text = b; }
    // b是data
    else { data = b; }
  }
  // 循环children数组
  if (is.array(children)) {
    for (i = 0; i < children.length; ++i) {
      // 若当前子元素是字符串或数字，则将其包装成vnode
      if (is.primitive(children[i])) children[i] = VNode(undefined, undefined, undefined, children[i]);
      // VNode(sel,data,children,text,elm,key)
    }
  }
  // tagName若为svg，则特殊处理
  if (sel[0] === 's' && sel[1] === 'v' && sel[2] === 'g') {
    addNS(data, children, sel);
  }
  // 返回VNode
  return VNode(sel, data, children, text, undefined);
};
```

<!--more-->

# patch 方法 (即snabbdom.init()的返回方法)

> 此方法是snabbdom的核心,定义在snabbdom.js中

### 逻辑梳理 

- 此方法接收两个参数 —— oldVnode, vnode
  
- patch 方法会对第一个参数oldVnode进行处理,若oldVnode 不是 virtual dom 而是原生dom ,则将其包装成 vdom

- 若oldVnode 与 vnode 被判定为同一个vnode(根据key与sel属性判断,若这两者都分别相等,则为同一个),执行patchNode方法

```js
function sameVnode(vnode1, vnode2) {
  // key相同并且 sel想用判定为同一个Vnode
  return vnode1.key === vnode2.key && vnode1.sel === vnode2.sel;
}
```

# patchNode 方法

> 定义在snabbdom.js中

### 逻辑梳理 

- 接受三个参数 —— oldVnode, vnode, insertedVnodeQueue

- 若oldVnode===vnode,直接return

- 若oldVnode 与 vnode 被判定为同一个vnode

    1. 若Vnode 存在有text属性(根据h方法可知vnode的children与text只存在一种),讲dom元素的contentText置换为vnode.text
    
    2. 若Vnode 不存在text属性
    
        ①  oldVnode与 vnode都只存在children 且两者不相等,则update children
        
        ②  vnode 有 children ,若oldVnode存在text 则 将dom元素的contentText置空,并addVnodes(children)
        
        ③  oldVnode 有 children ,则将children remove
        
        ④  oldVnode与 vnode都不存在children,oldVnode存在text, 则 将dom元素的contentText置空
        
        
```js
function patchVnode(oldVnode, vnode, insertedVnodeQueue) {
    var i, hook;
    // &&前面的运算通过后才会运行&&后面的 称为短路
    if (isDef(i = vnode.data) && isDef(hook = i.hook) && isDef(i = hook.prepatch)) {
      i(oldVnode, vnode);
    }
    var elm = vnode.elm = oldVnode.elm, oldCh = oldVnode.children, ch = vnode.children;

    if (oldVnode === vnode) return;
    // 完全相等 return
    if (!sameVnode(oldVnode, vnode)) {
      // 若不是同一个Vnode

      // 获取父级dom
      var parentElm = api.parentNode(oldVnode.elm);

      // 根据vnode 和 insertedVnodeQueue 建原生dom
      elm = createElm(vnode, insertedVnodeQueue);

      api.insertBefore(parentElm, elm, oldVnode.elm);
      removeVnodes(parentElm, [oldVnode], 0, 0);
      return;
    }
    // 若是同一vnode
    if (isDef(vnode.data)) {
      // 新node存在data属性
      for (i = 0; i < cbs.update.length; ++i) cbs.update[i](oldVnode, vnode); //分别更新class style props event
      i = vnode.data.hook;
      if (isDef(i) && isDef(i = i.update)) i(oldVnode, vnode);
    }
    // 若新node没有text
    if (isUndef(vnode.text)) {
      // 旧node与新node都存在children元素 且两者不相等
      if (isDef(oldCh) && isDef(ch)) {
        if (oldCh !== ch) updateChildren(elm, oldCh, ch, insertedVnodeQueue);//更新children
      } else if (isDef(ch)) {
        // 存在ch，不存在oldch
        if (isDef(oldVnode.text)) api.setTextContent(elm, '');
        // 旧元素的text设置为空，并添加Vnode
        addVnodes(elm, null, ch, 0, ch.length - 1, insertedVnodeQueue);
      } else if (isDef(oldCh)) {
        // 不存在ch，存在oldch
        // 将node的children移除
        removeVnodes(elm, oldCh, 0, oldCh.length - 1);
      } else if (isDef(oldVnode.text)) {
        // 若旧node存在text 则移除
        api.setTextContent(elm, '');
      }
    } else if (oldVnode.text !== vnode.text) {
      api.setTextContent(elm, vnode.text);
    }
    if (isDef(hook) && isDef(i = hook.postpatch)) {
      i(oldVnode, vnode);
    }
  }

```
       