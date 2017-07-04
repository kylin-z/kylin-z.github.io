---
title: js原生实现cookie的读写和删除
date: 2016-03-06 21:21:44
tags: [前端,javascript,原生,cookie]
categories: 前端
---

之前做忘记密码功能一直用的jquery的cookie插件，这一次尝试用js原生来写，发现也并不复杂。
最简单的实现：
```
function getCookie(name)
        {
            var arr,reg=new RegExp("(^| )"+name+"=([^;]*)(;|$)");
            if(arr=document.cookie.match(reg))
                return unescape(arr[2]);
            else
                return null;
        }
        function setCookie(name,value){
            document.cookie = name+"="+value;
        }
        function delCookie(name){
            setCookie(name,"");
        }
```
然而这个实现方法并不能真正删除cookie，只是将其置空

<!--more-->

下面是w3c官网的代码:
```
        //设置cookie
        function setCookie(cname, cvalue, exdays) {
            var d = new Date();
            d.setTime(d.getTime() + (exdays*24*60*60*1000));
            var expires = "expires="+d.toUTCString();
            document.cookie = cname + "=" + cvalue + "; " + expires;
        }
        //获取cookie
        function getCookie(cname) {
            var name = cname + "=";
            var ca = document.cookie.split(';');
            for(var i=0; i<ca.length; i++) {
                var c = ca[i];
                while (c.charAt(0)==' ') c = c.substring(1);
                if (c.indexOf(name) != -1) return c.substring(name.length, c.length);
            }
            return "";
        }
        //清除cookie
        function clearCookie(name) {
            setCookie(name, "", -1);
        }

```
没有删除已有cookie的直接方法，所以通过设置失效时间来做。将失效时间设置为过去，那么就能确保删除cookie
