---
title: 如何在canvas中使用自定义字体图标
date: 2017-11-21 17:25:00
tags: [canvas,'@font-face']
categories: [canvas]
---
## 案例
### 以`font-awesome`这个图标库为例
<p class="tip">字体图标的展示需要保证图标代码为unicode,需要在code前面加上`\u`</p>
```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport"
        content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/font-awesome/4.5.0/css/font-awesome.min.css">
  <title>Document</title>
</head>
<body>
<canvas id="canvas" width="800" height="400"></canvas>
<i class="fa fa-hand-pointer-o"></i>
<input type="button" value="点我画图标" id="button">
<script>

  function draw(){
    var canvas=document.getElementById("canvas");
    var ctx=canvas.getContext("2d");
    ctx.font='48px FontAwesome';
    ctx.fillText('\uF064 \uF065 \uF0a5',20,75);
  }
  document.getElementById('button').onclick = draw
</script>
</body>
</html>

```
<a class="jsbin-embed" href="http://jsbin.com/cohurap/embed?html,js,output">JS Bin on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?4.1.1"></script>