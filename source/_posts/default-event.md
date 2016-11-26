---
title: 如何在内层div滚动到底部时禁止外部div滚动
date: 2016-03-23 21:05:41
tags: [前端,事件,原生,浏览器默认事件,事件冒泡]
categories: 前端
---
做过微信端(特别是IOS)web开发的小伙伴一定遇到过这样一个问题:
微信内嵌浏览器的顶部是可以下拉的(ios下底部还能上拉),会显示网页有XXXX提供，QQ浏览器X5内核提供技术支持
平时看到这个可能觉得还好，但若是下面我们自己的网页中有一个需要滚动的div(overflow-y:scroll)时，这个就极其恶心了，拉倒底部时再往下拉，会导致微信浏览器顶部下拉(ios拉倒顶部时上拉则会导致整个页面上拉，下方露出一大片黑色)
这是因为内部div滚动到头之后会带动webview滚动
我们可以通过禁止默认事件来解决，当滚动到底部时，判断touchmove是否是向下滚动，若是，则event.preventDefault()，反之亦然
具体代码如下
<!--more-->

``` html
<body>
	<div class="box" id="box">
		<div class="title">选择项目</div>
		<div class="button_group" id="group">
			<button>1</button>
			<button>2</button>
			<button>3</button>
			<button>4</button>
			<button>5</button>
			<button>6</button>
			<button>7</button>
			<button>8</button>
			<button>9</button>
			<button>10</button>
			<button>11</button>
			<button>12</button>
		</div>
	</div>
	<script>
		var $box = document.getElementById("box");
		var $group = document.getElementById("group");
		var posY = 0;
		var realHeight = $group.scrollHeight;
		$box.ontouchmove = function(e){
			e = e||window.event;
			e.preventDefault();
		}
		$group.addEventListener('touchstart',function(e){
			posY = e.targetTouches[0].clientY;
		}) 
		$group.addEventListener('touchmove', function(e){
			e = e||window.event;
			var this_posY = e.targetTouches[0].clientY;
			var sHeight = e.currentTarget.scrollTop;
			var thisHeight = sHeight + 400;//400是div的高度
			if(thisHeight>=realHeight && posY>this_posY){
				e.preventDefault();
			}else if(sHeight==0 && posY<this_posY){
				e.preventDefault();
			}
			posY = this_posY;
			e.stopPropagation();
		});
	</script>
</body>
```