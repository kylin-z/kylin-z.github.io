---
title: jsԭ��ʵ��cookie�Ķ�д��ɾ��
date: 2016-03-06 21:21:44
tags: ǰ�� js ԭ�� cookie
---

֮ǰ���������빦��һֱ�õ�jquery��cookie�������һ�γ�����jsԭ����д������Ҳ�������ӡ�
��򵥵�ʵ�֣�
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
Ȼ�����ʵ�ַ�������������ɾ��cookie��ֻ�ǽ����ÿ�

������w3c�����Ĵ���:
```
        //����cookie
        function setCookie(cname, cvalue, exdays) {
            var d = new Date();
            d.setTime(d.getTime() + (exdays*24*60*60*1000));
            var expires = "expires="+d.toUTCString();
            document.cookie = cname + "=" + cvalue + "; " + expires;
        }
        //��ȡcookie
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
        //���cookie
        function clearCookie(name) {
            setCookie(name, "", -1);
        }
        
```
δ�����������