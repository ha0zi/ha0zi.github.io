---
title: '计算小技巧'
date: 2021-10-23T18:36:18+08:00
draft: false
categories: ["JAVA"]
tags: ["基础","小技巧","计算"]
---
### 说明
本文准备长期更新，以记录工作、学习中发现的一些关于计算的小技巧。

目前仅掌握了 JAVA，以后的案例随着掌握的语言可能不限于 JAVA

<!-- more -->
1. 当除数为 10 的 n 次幂;

提防精度丢失，可以考虑使用字符串分割然后拼接的方式获取结果
```java
// 计算 432843 / 10^3
int a = 432843;

String aStr = String.valueOf(a);
Strint aLen = aStr.subtring(0, 3)
String aReg = aStr.subtring(3, 7);

return Integer.valueOf(aLen + "." + aReg);

```
