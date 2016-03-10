---
title: ios输入法的keyup事件失效的解决方法
date: 2016-03-10 22:49:29
tags:[前端,事件,原生]
categories: 前端
---
如下图所示,若果直接点击红框部分输入文字的话将不触发keyup事件，这时可以改用原生的oninput事件,chrome等浏览器可以这样处理
![Alt text](http://7xrqm7.com1.z0.glb.clouddn.com/QQ%E5%9B%BE%E7%89%8720160310225153.png)
```
var this_obj = document.getelementbyid(x);
obj.oninput = function(){
    //事件内容
}
```

