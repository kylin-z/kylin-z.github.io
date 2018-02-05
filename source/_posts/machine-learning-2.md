---
title: 李宏毅machine learning学习笔记——Regression
date: 2017-11-26 21:17:48
tags: [machine learning]
categories: [machine learning学习笔记]
---
## 资料

|pdf|video|B站|
|---|---|---|
| [链接](http://speech.ee.ntu.edu.tw/~tlkagk/courses/ML_2017/Lecture/Regression.pdf)|[链接](https://www.youtube.com/watch?v=fegAeph9UaA)|[链接](https://www.bilibili.com/video/av10590361/?from=search&seid=11735940949684226997#page=3)|

## 例子:预测宝可梦进化后的武力值(cp)

### 输入
- 用`X`来表示一只宝可梦
- 用`Xi`来表示宝可梦进化前的种种特性(feature)
 - `Xcp` 现在的cp值
 - `Xs` 物种
 - `Xhp` hp值
 - `Xw` 体重
 - `Xh` 身高

### 输出
- 这只宝可梦进化后的cp值：`Y`

### Model

<p class="tip">假设输入与输出间存在`Y = w·Xcp` 这样的关系(这里省略了X的其他属性)</p>

由于`w`、`b`可以为任何值。这里我们将各种数值带入到`b`、`w`中，就得到了一个Model(A set of function)
- `f1:Y = 10+9·Xcp`
- `f2:Y = 9.8+9.2·Xcp`
- `f3:Y = - 0.8-1.2·Xcp`
- ...

最后得到一个Linear Model
![](http://ozm59jgdk.bkt.clouddn.com/Regression-linear%20mode.png)

<!-- more -->

### Loss function 
`Loss function `用来衡量`Model`中的`function`，它的`Input`是`Model`中的`fn`，它的`Output`可以是这个`fn`与正确的数值的差平方和。
由于`fn`是由`b`、`w`的数值决定的，由此可得`Loss function L(w,b)`如下图所示

![](http://ozm59jgdk.bkt.clouddn.com/Loss%20function.png)

### 找到最优`f*`——Gradient Descent(梯度下降法)

> `f*`是Model中的一个f，使Loss function能取到最小值

<p class="tip"> 假设只有一个参数w </p>

1. 寻找一个w0的位置，计算L在w0的位置的微分dL/dw|w=w0。
2. 移动w的到w1的位置，w1 <- w0 - η*dL/dw|w=w0 (η被称为learning rate——步长，是一个可设置的常量)如果当前位置的微分的绝对值很大，则说明距离目标点越远，反之则越近，等于0的时候不再移动。
3. 直到找到一个wt，使dL/dw|w=wt = 0
![](http://ozm59jgdk.bkt.clouddn.com/%E6%A2%AF%E5%BA%A6%E4%B8%8B%E9%99%8D%E6%B3%95.png)


<p class="tip">如下图所示，梯度下降法并不能够保证一定能找到global minima或是local minima，找到的也可能是saddle point</p>

![](http://ozm59jgdk.bkt.clouddn.com/%E6%A2%AF%E5%BA%A6%E4%B8%8B%E9%99%8D%E6%B3%952.png)

> 回到两个参数的情况，将所有的偏微分排成一个向量，这个向量就被称为梯度

![](http://ozm59jgdk.bkt.clouddn.com/%E6%A2%AF%E5%BA%A6.png)

### 优化Model

> 通过以上的方式我们找到一个函数f `y = 188.4+2.7*xcp`,如图所示,并不是所有的点都能拟合函数，讲参数带入Loss function得到error为35.0 依然比较大。这时我们需要想办法优化我们的model。我们用相同的方法分别尝试了1-5次方程。从下图中可以看出，在training data中的error越来越小，但是在testing data上，误差变化却并不与在training data上一致，甚至在5次方程中，误差达到了200+，这个现象被称为`overfitting`

![二次](http://ozm59jgdk.bkt.clouddn.com/%E4%BA%8C%E6%AC%A1.png)
![三次](http://ozm59jgdk.bkt.clouddn.com/%E4%B8%89%E6%AC%A1.png)
![四次](http://ozm59jgdk.bkt.clouddn.com/%E5%9B%9B%E6%AC%A1.png)
![五次](http://ozm59jgdk.bkt.clouddn.com/%E4%BA%94%E6%AC%A1.png)
![overfitting](http://ozm59jgdk.bkt.clouddn.com/overfitting.png)

<p class="tip">function 并不是越复杂越好。</p>


## tips
- 是否是现行模型取决于`w`和`b`，`y = b+w1*xcp + w2*xcp^2` 依然是一个linear model
- 在`linear regression`的场景中不用担心`wt`会出现在`saddle point`的位置

