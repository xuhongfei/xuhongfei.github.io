---
layout:	post
title:	iOS精度数据处理之NSDecimalNumber
date:	2018-11-21 21:30:00 +0800
categories: original
---

最近在开发一款金融相关的产品，项目中遇到高精度数据处理的问题。原本是使用float、double来处理此类数据，但总是不精确的。针对高精度的数据我们总是希望从接口获取数据的时候是以字符串的形式返回，如果此类数据仅仅是用来显示输出的话，字符串是可以适应大部分场景的；如果存在高精度数据计算（加减乘除指数次方）、高精度数据比较，使用float、double则会出现误差。

如下图所示：

![img](https://diycode.b0.upaiyun.com/photo/2018/dfb0056d118cee567f42ae5e81afc922.png)

iOS中可以使用NSDecimalNumber来进行精确计算。

![img](https://diycode.b0.upaiyun.com/photo/2018/eaa99950b6db7e62bb639dd2241d7314.png)

NSDecimalNumber进行数据计算

![img](https://diycode.b0.upaiyun.com/photo/2018/e529b7bbc54cf95dbdef9b1d1a2cc8e3.png)

在项目还碰到一个情况，两个NSDecimalNumber在进行Multiplying或者Dividing计算时（其他运算类同），往往需要精确到一定的位数，如果仅仅使用另一个NSDecimalNumber来接收乘法和除法的结果，精度却不如预期的结果：

![img](https://diycode.b0.upaiyun.com/photo/2018/69eeefa928ae91f6ba41560bb57dc0a1.png)

计算结果小数点后有14位，而实际项目中可能只需要保留6位、8位。此时就需要使用NSDecimalNumberHandler。

![img](https://diycode.b0.upaiyun.com/photo/2018/9aefaa1610eb91cde22684b03c6511c2.png)

得到一个四舍五入小数点后精度为6的结果。
至于小数保留的类型则根据roundingMode来设定：

![img](https://diycode.b0.upaiyun.com/photo/2018/d77c613f40e8e8de6affe6dedf7b888a.png)

至此，可以处理好iOS中常见的高精度数据处理问题。