---
title: 最长公共子序列(LCS)
date: 2018-02-02 17:12:04
tags: [算法]
categories: 算法
---

## 基本概念

>（Longest Common Subsequence LCS）是从给定的两个序列X和Y中取出尽可能多的一部分字符，按照它们在原序列排列的先后次序排列得到。LCS问题的算法用途广泛，如在软件不同版本的管理中，用LCS算法找到新旧版本的异同处;在软件测试中，用LCS算法对录制和回放的序列进行比较，在基因工程领域，用LCS算法检查患者DNA连与键康DNA链的异同;在防抄袭系统中，用LCS算法检查论文的抄袭率。LCS算法也可以用于程序代码相似度度量，人体运行的序列检索，视频段匹配等方面，所以对LCS算法进行研究具有很高的应用价值。

### 子序列(subsequence)
一个特定序列的子序列就是将给定序列中零个或多个元素去掉后得到的结果(不改变元素间相对次序)。例如序列`<A,B,C,B,D,A,B>`的子序列有：`<A,B>`、`<B,C,A>`、`<A,B,C,D,A>`等。

### 公共子序列(common subsequence)
给定序列X和Y，序列Z是X的子序列，也是Y的子序列，则Z是X和Y的公共子序列。例如X=[A,B,C,B,D,A,B]，Y=[B,D,C,A,B,A[，那么序列Z=[B,C,A]为X和Y的公共子序列，其长度为3。但Z不是X和Y的最长公共子序列，而序列[B,C,B,A]和[B,D,A,B]也均为X和Y的最长公共子序列，长度为4，而X和Y不存在长度大于等于5的公共子序列。对于序列[A,B,C]和序列[E,F,G]的公共子序列只有空序列[]。

### 最长公共子序列
给定序列X和Y，从它们的所有公共子序列中选出长度最长的那一个或几个。

### 子串
将一个序列从最前或最后或同时删掉零个或几个字符构成的新系列。区别与子序列，子序列是可以从中间抠掉字符的。cnblogs这个字符串中子序列有多少个呢？很显然有27个，比如其中的cb,cgs等等都是其子序列

![](http://7xrqm7.com1.z0.glb.clouddn.com/LCS.png)

## 分析

求解LCS问题，不能使用暴力搜索方法。一个长度为n的序列拥有 2的n次方个子序列，它的时间复杂度是指数阶，太恐怖了。我们先对LCS问题进行特征分析。

结合下图，设A=“a0，a1，…，am”，B=“b0，b1，…，bn”，且Z=“z0，z1，…，zk”为它们的最长公共子序列。不难证明有以下性质：
- 如果am=bn，则zk=am=bn，且“z0，z1，…，z(k-1)”是“a0，a1，…，a(m-1)”和“b0，b1，…，b(n-1)”的一个最长公共子序列；
- 如果am!=bn，则若zk!=am，蕴涵“z0，z1，…，zk”是“a0，a1，…，a(m-1)”和“b0，b1，…，bn”的一个最长公共子序列；
- 如果am!=bn，则若zk!=bn，蕴涵“z0，z1，…，zk”是“a0，a1，…，am”和“b0，b1，…，b(n-1)”的一个最长公共子序列。

![](http://7xrqm7.com1.z0.glb.clouddn.com/LCS-example.jpeg)

由以上特征可得到递归公式如下:
<p class="tip">c[i,j]表示长度为i的S1和长度为j的S2的LCS的长度</p>

![](http://7xrqm7.com1.z0.glb.clouddn.com/LCS-formula.jpeg)

<!-- more -->

## 计算LCS长度

我们先不贴代码，将计算LCS长度的过程一步步分解开来。

- 初始化

  图中白底格子所填的数字为c[i,j]的值，表示长度为i的S1和长度为j的S2的LCS的长度。
  首先将表格初始化，当i==0 || j==0时，显然LCS的长度也为0。

  ![](http://7xrqm7.com1.z0.glb.clouddn.com/LCS-init.jpg)

- 依次填值
  当i=1，j=1~9时，S1(i)与S2(j)并不存在相等，c(i,j)的值等于左侧格子与上测格子中较大的一个的值。
  ![](http://7xrqm7.com1.z0.glb.clouddn.com/LCS-step1.png)

  当i=2，j=1时，S1(i)与S2(j)相等,c(i,j)的值等于左上方格子的值+1。
  ![](http://7xrqm7.com1.z0.glb.clouddn.com/LCS-step2.png)

  按照递归公式，依次将表格填完，最后下方的格子的值(c(8,9))即为S1、S2的最长公共子序列的长度。
  ![](http://7xrqm7.com1.z0.glb.clouddn.com/LCS-step3.png)

## 推导LCS

  如图所示:
  ![](http://7xrqm7.com1.z0.glb.clouddn.com/LCS-step4.png)

  - 从(8,9)位置开始反推，案例中S1(8) != S2(9)，即c(8,9)是由c(8,8)和c(7,9)两个格子中较大的值即c(8,8)决定的。
  - S1(8) == S2(8)，将 S1(8)的值记录下来，c(8,8)的值来源于c(7,7)。
  - 继续推导，遇到相等，则将值记录下来，直到最左上角。

  <p class="tip">由于LCS可能不止一个，当遇到S1(i) != S2(j) && c(i-1,j) == c(i,j-1)时，要将两种可能性都考虑到。</p>

  最后得到如图5条路线，并将每条路线经过的红色点在S1、S2中的值取出并去重，得到最后的LCS = ['34678','35678','35778']

## 代码

```js
function LCS(a, b) {

  var a_arr = [...a]
  var b_arr = [...b]
  var w = a_arr.length
  var h = b_arr.length

  var result_arr = []

  for (let i = 0; i < h; i++) {
    let temp = []
    for (let j = 0; j < w; j++) {
      let a_elm = a_arr[j]
      let b_elm = b_arr[i]

      let left_lcs = temp[j - 1] || 0

      let up_lcs = (result_arr[i - 1] && result_arr[i - 1][j]) || 0
      let up_left_lcs = (result_arr[i - 1] && result_arr[i - 1][j - 1]) || 0

      if (a_elm === b_elm) temp.push(up_left_lcs + 1)
      else temp.push(Math.max(left_lcs, up_lcs))
    }
  
    result_arr.push(temp)
    console.log(temp)
  }

  var length = result_arr[h-1][w-1]

  console.log(length)

  var result = []

  function currentLCS(i, j) {

    if (i < 0 || j < 0) return [[]]

    let a_elm = a_arr[j]
    let b_elm = b_arr[i]


    let left_lcs = (result_arr[i] && result_arr[i][j - 1]) || 0
    let up_lcs = (result_arr[i - 1] && result_arr[i - 1][j]) || 0


    if (a_elm === b_elm) {
      return currentLCS(i - 1, j - 1).map(o => o.concat(a_elm))
    } else if (left_lcs > up_lcs) {
      return currentLCS(i, j - 1)
    } else if (left_lcs < up_lcs) {
      return currentLCS(i - 1, j)
    } else {
      return currentLCS(i - 1, j).concat(currentLCS(i, j - 1))
    }
  }
  var lcs_arr_temp = currentLCS(h-1, w-1).map(o => o.join(''))
  return lcs_arr_temp.filter((o, i) => {
    return lcs_arr_temp.indexOf(o) === i
  })
}

LCS("13456778", "357486782")
//["35778", "35678", "34678"]
```



