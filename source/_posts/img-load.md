---
title: img的onload与complete
date: 2016-03-09 22:14:54
tags: [前端,图片加载]
categories: 前端
---
今天遇到了一个奇怪的bug
需要将图片裁剪至合适的大小，然后我这样一个方法
```
var x = 65;
if(width>height)
    $(this).css("height",x);
else
    $(this).css("width",x);
    
```
由于各种原因`<img>`元素是通过字符串在js里面拼接出来的,上面那个方法用在append到界面上之后
刚做完的时候貌似没有问题,但是多刷新几次之后,神奇的事情发生了,这个方法居然时灵时不灵！

后来通过研究发现,这个方法应该在图片加载完之后调用。
下面是改良之后的方法:

<!--more-->

``` js
(function ($) {
    function autoSize(obj, w, h, is_cut) { //is=true 不移位
        var oIMG = new Image();
        oIMG.onload = function () {
            var oW = this.width;
            var oH = this.height;
            if (is_cut && w == h) {
                if (oW > oH) {
                    obj.style.height = w + 'px';
                }
                else {
                    obj.style.width = w + 'px';
                }
            }
        };
        oIMG.src = obj.src;
    }

    $.fn.autoSize = function (width, height, is_cut) {
        this.each(function (i) {
            $(this).load(function () {
                return autoSize(this, width, height, is_move, is_cut);
            });
        });
        return this;
    };
})(jQuery);

```
定义了图片的onload事件,在图片加载完之后执行。

这里需要注意一个细节
不要把`oIMG.src=obj.src;`这一句紧跟在`var oIMG = new Image();`之后。

除了这个方法之外,还可以使用图片的complete 属性，如用以下方法获取图片的尺寸
```
var imgLoad = function (url) {
    var img = new Image();
    img.src = url;
    if (img.complete) {
        callback(img.width, img.height);
    } else {
        img.onload = function () {
            callback(img.width, img.height);
            img.onload = null;
        };
    };
};
```
