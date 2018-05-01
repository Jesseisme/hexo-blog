---
title: 达夫设备(Duff's device)
date: 2018-05-01 14:10:24
categories: 人生很美好
tags:
  trick
---

## 达夫设备是什么？
在看《js高性能编程》这一本书的过程中，发现了其中一个处理循环的算法，书中称之为达夫设备。这到底是什么呢？来看看wiki的解释  
**达夫设备（英文：Duff's device）是串行复制（serial copy）的一种优化实现，通过汇编语言编程时一常用方法，实现展开循环，进而提高执行效率。这一方法据信为当时供职于卢卡斯影业的汤姆·达夫于1983年11月发明，并可能是迄今为止利用C语言switch语句特性所作的最巧妙的实现。**
[达夫设备](https://zh.wikipedia.org/wiki/%E8%BE%BE%E5%A4%AB%E8%AE%BE%E5%A4%87)
说白了就是展开循环的变形

<!-- more -->

### 达夫设备的一个例子
```javascript
var a = [0, 1, 2, 3, 4];
var sum = 0;
for(var i = 0; i < 5; i++)
  sum += a[i];
console.log(sum);

// 展开循环
var a = [0, 1, 2, 3, 4];
var sum = 0;
sum += a[0];
sum += a[1];
sum += a[2];
sum += a[3];
sum += a[4];
console.log(sum);
```
因为少做了循环，所以下面的代码执行效率高，而且随着循环数据越来越多，下的的执行效率会比上面的代码优势更大。
看一看完整版的达夫设备例子：
```javascript
var iterations = 4000
var testVal = 0
// 原始循环
function doSlow()
{
   /* common code */

   for (l=1;l<iterations+1;l++)
   {
      testVal++;
   }

   /* common code */
}

// 达夫设备
function doDuffs()
{
   /* common code */

   n = iterations % 8;
   while (n--)
   {
      testVal++;
   }

   n = parseInt(iterations/8);
   while (n--)
   {
      testVal++;
      testVal++;
      testVal++;
      testVal++;
      testVal++;
      testVal++;
      testVal++;
      testVal++;
   }

   /* common code */
}
```
可以看出达夫设备将每次循环化解为隔八次循环一次，并非每个循环次数都能被8整除，上面的**while**循环是处理余数次数，因为少了循环次数，所以能提高性能。
### 性能
[javascript_optimization](https://andrew.hedges.name/experiments/javascript_optimization/)可以在这个网站测试达夫设备的性能，下面是一个简单的性能截图，可以看到性能差距不是很大，但还是达夫设备快了一点点  
![达夫设备](达夫设备.png)

### 总结
达夫设备不是银弹，只是一个小trick，性能提升的不是很明显。可能在老的机器上面有明显提升，而在新的浏览器针对循环性能做了优化后，优势全无。建议不要在实际代码中运用，导致代码可读性下降，得不偿失。