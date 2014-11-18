---
layout: post
title: "Javascript 取数组最大最小值及百分数转化"
category: Technology
tags: Javascript
---

### 取数组中Max和Min
要用Javascript实现如下功能

    [1,2,3].max()// => 3 
    [1,2,3].min()// => 1

可通过简单地封装
[Math.max](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Math/max)/[Math.min](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Math/min) 来实现
    Array.prototype.max = function() {
      return Math.max.apply(null, this)
    }

    Array.prototype.min = function() {
      return Math.min.apply(null, this)
    }

当然你也可以直接调用

    var min = Math.min.apply(null, arr),
        max = Math.max.apply(null, arr);

### 百分数转化
    Number.prototype.toPercent = function(n) {
      n = n || 2;
      return ( Math.round(this * Math.pow( 10, n + 2 ) ) / Math.pow( 10, n ) ).toFixed( n ) +'%';}

    var num = 0.358975
    num.toPercent(3) // "35.898%"
