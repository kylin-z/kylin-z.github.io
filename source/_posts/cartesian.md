---
title: 计算笛卡尔积
date: 2017-03-09 15:02:03
tags: [笛卡尔积,javascript]
categories: 笔记
---
 ## 计算笛卡尔积

 ```js
 function cartesianProductOf() {
  return Array.prototype.reduce.call(arguments, function(a, b) {
    var ret = [];
    a.forEach(function(a) {
      b.forEach(function(b) {
        ret.push(a.concat([b]));
      });
    });
    return ret;
  }, [[]]);
}
 ```

 ### 使用方法
 ```js
 cartesianProductOf(['1','3'],['a','b'])
 ```
