---
title: 李宏毅machine learning学习笔记——导论
date: 2017-11-18 20:14:34
tags: [machine learning]
categories: [machine learning学习笔记]
---
## 前言
最近在学习machine learning相关的知识，李宏毅教授的机器学习课程是一套非常好的入门教材，这里使用的2017年的版本。
[课程日历](http://speech.ee.ntu.edu.tw/~tlkagk/courses_ML17_2.html)
[B站视频](https://www.bilibili.com/video/av10590361/index_2.html?t=61#page=1)

## 几个概念之间的关系
- 人工智能
  人们想要达成的目标
- 机器学习(machine learning)
  达成人工智能的一种手段
- 深度学习(deep learning)
  机器学习其中的一个方法

![几个概念之间的关系](http://ozm59jgdk.bkt.clouddn.com/%E5%87%A0%E4%B8%AA%E6%A6%82%E5%BF%B5%E7%9A%84%E5%85%B3%E7%B3%BB.png)

## machine learning
<p class="tip">machine learning:让机器具有一个能力，这个能力可以根据提供的资料，寻找到一个人们需要的function</p>

### machine learning的步骤

以supervise learning为例

1. 定义一个Model ——  define a set of function (f1、f2……)
2. 准备一些训练资料 Training Data (正确的输入和输出`label`的集合)，使机器具有衡量function好坏的能力 —— goodness of function
3. 准备一个有效率的演算法寻找Model中最优的funciton(f*) —— pick the best function
<!-- more -->

![机器学习的步骤](http://ozm59jgdk.bkt.clouddn.com/%E6%9C%BA%E5%99%A8%E5%AD%A6%E4%B9%A0%E7%9A%84%E6%AD%A5%E9%AA%A4.png)

### learning map

![learning map](http://ozm59jgdk.bkt.clouddn.com/laerning%20map.png)

### 几种问题(橙色)

- Regression 回归问题 —— 期望的输出是一个数值(scalar)
- Classification 预测类别问题 —— 从两个或多个类别中找到输出
  - Binary Classification 二元(yes or no)
  - Multi-class Classification 多元 
- Structured Learning —— 期望的输出是具有结构的

### 几种Model(绿色)

- Linear Model 线性模型
- Non-linear Model 非线性模型
  - Deep Learning 深度学习
- SVM
- decision tree
- K-NN

### 几种情境

- Supervised Learning —— 有大量的Training Data
- Semi-supervised Learning —— 没有大量的具有output(label)的数据、但是有大量与问题有关但没有output(label)的数据
- transfer Learning —— 没有大量的具有output(label)的数据、但是有大量无关的数据
- Unsupervised Learning —— model完全不用到任何有label的数据的情境
- Reinforcement Learning —— 没有正确的答案，机器拥有的只有一个相对的分数，与人类的真实情境较为接近









  

