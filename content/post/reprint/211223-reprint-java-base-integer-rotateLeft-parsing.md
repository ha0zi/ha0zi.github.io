---
title: 'Java rotateLeft 和 rotateRight 方法'
date: 2021-12-23T14:08:19+08:00
draft: false
categories: ["JAVA"]
tags: ["JAVA","转载","算法"]
---

这篇博客很清楚的解答了我对 Integer.rotateLeft() 方法的理解，在这之前我花费了好几个小时找了多篇博客都没有说到重点。   [博客原文](https://www.it610.com/article/1294323857599045632.htm)

#### 正文

这两个方法实现的思想是循环左移和循环右移。首先来理解这两个概念

> 循环移位就是把数值变成二进制，然后循环移动的过程；换句话说，循环移位就是将移出的低位放到该数的高位（循环右移）或把移出的高位放到该数的低位（循环左移），左移，和右移动都是对整数进行的操作，在Win32控制台应用程序中，整形占4Byte节32bit。

<!-- more -->

 **循环左移的过程：**
![循环左移的过程](https://img.it610.com/image/info8/861e6bb2242743a9b2a223607f1dc63f.png)

>  循环左移的过程可以分为3步：
>
> 1. 将x左端的n位先移动到y的低n位中，`x>>(32-n);`
> 2. 将x左移n位，其右面低位补0，`x< `
> 3. 进行按位或运算`(x >> (32 - n) | (x << n));`

<p style='color:red;font-size:13px;'> 看到 x >> (32-n)的如果觉得与代码写得不符，先憋着，往后看。</p>

---

**循环右移的过程：**

![循环右移的过程](https://img.it610.com/image/info8/dd8890ab91224ce1b70788e763596f94.png)

 > 循环右移的过程可以分为3步：
    1. 将x的左端的低n位先移动到y的高n位中 `x<<(32-n)`
          2. 将x右移n位，其左面高n位补 `0x>>n; `
              3. 进行按位或操作 `(x << (32 - n) | (x >> n));`

---

上代码:

```java
public static long rotateLeft(long i, int distance) {
    return (i << distance) | (i >>> -distance);
}
public static long rotateRight(long i, int distance) {
    return (i >>> distance) | (i << -distance);
}
```

实现的代码量可以说已经精简到最少了，有一点要注意的是，循环移位时，参数distance可以接受负数，当 `distance` 为负数时，这个等式是成立的，`rotateLeft(i, distance) = rotateRight(i,  -distance)`。这个方法中有两点值得借鉴的，第一从整体上讲循环移位的实现方式；第二是 `distance` 与 `-distance` 的巧妙运用。

   就拿循环左移先来说说第二点吧，前置条件，我们首先假设 `distance` 大于 *0*，起先我是很不理解 `i >>>  -distance` 的，后来在 *stackoverflow* 上发问，有人给出了解释，在移位的时候，如果 `distance` 小于 *0*，会根据被移位数的长度进行转换。就比如说这里我们对 `long` 进行移位，那么 `-distance` 就会被转换成 `(64 + distance)` (注，这里的 `distance` 是小于 *0* 的)。这样的话，如果 `distance` 大于 *0* 时，`(i <<  distance) | (i >>> -distance);` 就会被转化成 `(i << distance) | (i  >>> 64 + distance);`

  清楚了第二点，那么第一点也就不难理解了。用一幅图来解释循环左移。

![java rotateLeft()和rotateRight()方法_第1张图片](https://img.it610.com/image/info8/798cebc04d7f444683979fd7ff839d27.png)

在 `distance` 大于 *0* 的前提下，先左移 `distance` 位，然后再右移 `64-distance`，最终用或运算相加，就是循环移位的结果。图中为了省事儿用了 *8* 位做了个演示，先左移 *3* 位，然后右移 *(8-3)* 位，或运算之后就是结果啦。

---

下面是个更给力的方法-reverse(long i)，可以说就是高效率的化身。

```java
 public static long reverse(long i) {
     // HD, Figure 7-1
     i = (i & 0x5555555555555555L) << 1 | (i >>> 1) & 0x5555555555555555L;
     i = (i & 0x0f0f0f0f0f0f0f0fL) << 4 | (i >>> 4) & 0x0f0f0f0f0f0f0f0fL;
     i = (i & 0x00ff00ff00ff00ffL) << 8 | (i >>> 8) & 0x00ff00ff00ff00ffL;
     i = (i << 48) | ((i & 0xffff0000L) << 16) | ((i >>> 16) & 0xffff0000L) | (i >>> 48);
     return i;
 }
```

> 先来看一个例子：
>
> 二进制数反转
>
> 这里有一串数字：87654321，希望实现对他反转后的结果是：12345678。此时我的实现思路是：
>
> 第一次反转，一位一位的进行反转： 78563412   在这里我们实现了每两位的排序
>
> 第二次反转，既然每两位的已经排序了，那么现在就两位两位的进行反转：这时就变为了： 56781234 这里实现了每4位的排序
>
> 第三次反转：既然每四位已经排好序了，这时我们就每四位四位的进行反转：这时就变为了: 12345678。
>
> 这时就实现了排序的完成。

<p style="font-size:12px">从整体上说，这个 `reverse` 方法集移位与二分算法于一身，堪称经典。  第一步以一位为单位，奇偶位交换；第二步以两位为单位，完成前后两位的交换；第三步以四位为单位，完成前后四位的交换；第四步以八位为单位，完成前后八位的交换；最后一步没有按常理继续二分，而是通过一个转换一步就完成了以 *16* 和 *32* 位为单位的交换。进而结束了整个 *64* 位的反转。</p>

现在一步一步剖析都是如何实现的。

` i = (i & 0x5555555555555555L) << 1 | (i >>> 1) & 0x5555555555555555L;`

*16* 进制的 *5* 为 *0101*，或操作前半部分首先取出i的所有奇数位，然后整体左移一位，这样实现i的奇数位左移一位变成偶数位；或操作后半部分先右移，即将偶数位右移变成奇数位，然后再取出奇数位。这样就完成了64位中奇数位与偶数位的交换。

`i = (i & 0x3333333333333333L) << 2 | (i >>> 2) & 0x3333333333333333L;`

这句同样是实现交换，只不过3对应的16进制为0011，即本次交换以2个字节为单位，交换完成了4个字节的反转。`Liquid  error: Flag value is invalid: -O ””` 直到这行代码，实现了以字节为单位的反转，最后仅仅使用一行代码就实现了以两个字节为单位的反转。为了方便画图，现在对操作进行编号，另外从以字节为单位交换开始，之前的细节忽略。

```java
(i & 0x00ff00ff00ff00ffL) << 8 | (i >>> 8) & 0x00ff00ff00ff00ffL;
(i << 48)
(i & 0xffff0000L) << 16)
(i >>> 16) & 0xffff0000L)
(i >>> 48);
```

这幅图描述每个编号代码执行之后64位的变化。

![java rotateLeft()和rotateRight()方法_第2张图片](https://img.it610.com/image/info8/088ce1a0756b421e9ab032ac5d23fa96.png)

不多做解释，由于这个reverse的最后一行不是按常理”出牌”，所以我使用纯粹的二分法来实现reverse。

```java
public static long reverse(long i) {
    // HD, Figure 7-1
    i = (i & 0x5555555555555555L) << 1 | (i >>> 1) & 0x5555555555555555L;
    i = (i & 0x3333333333333333L) << 2 | (i >>> 2) & 0x3333333333333333L;
    i = (i & 0x0f0f0f0f0f0f0f0fL) << 4 | (i >>> 4) & 0x0f0f0f0f0f0f0f0fL;
    i = (i & 0x00ff00ff00ff00ffL) << 8 | (i >>> 8) & 0x00ff00ff00ff00ffL;
    i = (i & 0x0000ffff0000ffffL) << 16 | (i >>> 16) & 0x0000ffff0000ffffL;
    i = (i & 0x00000000ffffffffL) << 32 | (i >>> 32) & 0x00000000ffffffffL;
    return i;
}
```

至于为什么要采用那种方式，而不是用”纯粹”的二分法，在stackoverflow上有人提到，可能是因为前一种实现方式需要9个操作，而后一种实现方式需要10个操作。具体是出于怎样的目的可能只有作者才知道。关于reverse我在stackoverflow上的提问在[这里](https://stackoverflow.com/questions/9529275/how-does-i-48-i-0xffff0000l-16-i-16-0xffff0000l-i)。

最后看一个方法。

```java
public static int bitCount(long i) {
    // HD, Figure 5-14
    i = i - ((i >>> 1) & 0x5555555555555555L);
    i = (i & 0x3333333333333333L) + ((i >>> 2) & 0x3333333333333333L);
    i = (i + (i >>> 4)) & 0x0f0f0f0f0f0f0f0fL;
    i = i + (i >>> 8);
    i = i + (i >>> 16);
    i = i + (i >>> 32);
    return (int)i & 0x7f;
}
```

这个方法是返回一个long类型的数字对应的二进制中1的个数，其实google上有很多种，这里采用的叫平行算法实现的，算法如下图。

![java rotateLeft()和rotateRight()方法_第3张图片](https://img.it610.com/image/info8/64e4ef0dda1a46a48cd2a96d4e169f54.jpg)

但是这个方法的第一行又采取了一个特别的方式实现i中奇数位+偶数位，我有点儿没想明白。总体上就是像图中所示那样，相邻的两位相加的结果再相邻的四位相加，最后得到二进制中1的个数。还有一点值得提一下，就是最后一行与上7f，因为long类型，1的个数最多也不会超过64个，所以只取最后7位即可。
