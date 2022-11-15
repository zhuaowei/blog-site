---
title: "字符串的模式匹配算法 KMP"
date: 2022-11-15T20:02:06+08:00
draft: false
math: false

tags: ["数据结构", "算法", "字符串", "KMP"]
categories: ""
description: ""
---

## 1、KMP 简介

KMP 是1964年发明的字符匹配算法，经过 25 年的证明才将相关的理论证明完成。

> 假设要从字符串 str1 中匹配出字符串 str2。

暴力的字符匹配算法就是将两个字符串的字符从第一个开始一个一个匹配，如果不相等，str1 右移一个位置，而 str2 直接移动到 0，重新开始匹配，时间复杂度为 O(MN)。MN分别是 str2、str1 的长度。

而 KMP 算法就是将这个 str2 右移的速度加快了。具体是怎么加快的呢，就是利用 str2 中子串的**最长的前缀后缀相等的长度**进行加速的，它的时间复杂符不会超过O(2N)。

例如：`str1 = abbecabbstabbecabbx; str2 = abbecabby`。

加速匹配过程：

```
    i1
    v
    abbecabbstabbecabbx
    abbecabby
    ^
    i2
1、按顺序匹配
            i1
            v
    abbecabbstabbecabbx
    abbecabby
            ^
            i2
2、遇到不相等的情况，不是 i1 回退到字符 b 的位置，i2 回到索引为 0 位置，
而是 i1 不动，i2 向前移动到前面子串的最长前缀后缀相等长度的位置。
此时最长的相等的前缀后缀是 abb，（[abb]ec[abb]）
            i1
            v
    abbecabbstabbecabbx
    abbecabby
       ^
       i2
```

为什么不用重新匹配呢，因为经过 str2 的最长前缀后缀计算后，已经找到了能够移动的最大位置，而且保证中间不会再有匹配成功的位置。

继续匹配：

```
            i1
            v
    abbecabbstabbecabbx
    abbecabby
       ^
       i2
3、发现又不相等，i2 位置的最长前缀后缀长度为 0，跳转到索引 0 位置
            i1
            v
    abbecabbstabbecabbx
    abbecabby
    ^
    i2
4、也没匹配上，同时发现 i2 已经到了 0 位置了，不能再移动了，只好移动一下 i1
             i1
             v
    abbecabbstabbecabbx
    abbecabby
    ^
    i2
5、如此循环下去。
```

从上面的过程可以看出，i1 自始至终没有回退，i1 移动到 第三个 `a` 的位置，只需要 4 步（从i1、i2第一次不相等的位置）；而暴力匹配至少要 10 步。

## 2、最长前缀后缀
 
`abbecabby` 的子串是 `abbecabb`，子串的最长前缀后缀是 `abb`.

```
    abbecabb
    abb
         abb
```

i2 移动之前，i2 之前的字符串和 i1 前面那段与之长度相同的一段，是相等的。

```
            i1
            v
    abbecabbstabbecabbx
    abbecabby
            ^
            i2

=> abbecab(str1) = abbecab(str2)
```

把 i2 移动到最长前缀后缀的下一个位置与 i1 位置进行比较，保证了前缀和后缀相等，也就是 str2 的前缀和 str1 中 i1 前面的一段后缀与之相等。

```
            i1
            v
    abbecabbstabbecabbx
         ---
    abbecabby
    ---  ---
       ^
       i2
等价于
            i1
            v
    abbecabbstabbecabbx
         ---|
    |-> |abbecabby
         ---| ---
            ^
            i2
```

相当于 str1 从第二个 `abb` 开始与 str2 的 0 位置重新开始匹配，并且移动了 `abb` 的位置。因为这两段是肯定相等的，我们计算过了，我们只需要匹配不相等的部分，即下一个位置。利用反证法可以证明，跳过的位置不会再有更长的最长前缀后缀。

这样我们只需要求出 str2 的 next 数组即可。

## 3、next 数组求解

next数组是与 str2 等长的 int 型数组，它的 i 位置记录了前面子串的最长前缀后缀的长度（最长前缀尾部后面一个元素的索引），换句话说就是，当匹配不成功时，i2 应该跳到的位置。

next 数组规定 `next[0] = -1; next[1] = 0;`

`next[1] = 0`，因为子串长度为 1，并且前缀和后缀不允许等于子串长度，所以 `next[1]` 必定为 0。

当 i > 1 时如何求 `next[i]` 呢？

分两种种情况：

1、当子串的最长前缀后面的字符与 i - 1 的字符相等时，相当于最长前缀后缀增加了1，所以 `next[i] = next[i - 1] + 1`

2、当子串的最长前缀后面的字符与 i - 1 的字符不相等时，根据**子串最长前缀位置的子串的最长前缀**判断：

    2.1、当这个值 大于 0 时，最长前缀还可以再往前推，最长前缀变小。
    2.2、如果这个值 小于 0 时，说明最长前缀长度已经为 0 了，不能再推了，i 位置的最长前缀长度为 0，`next[i] = 0`

同样可以使用反证法证明在这中间已经没有比之更长的前缀了。

这个 str2 的 next 数组计算是线性的，所以，它的时间复杂度是 O(M)。KMP算法中使用了一次，初次之外还有一个时间复杂度为 O(N) 的循环匹配，所以准确地说，KMP 的总体复杂度是 O(M + N)，因为 N >= M，所以上面说时间复杂度不超过 O(2N) 也对。

## 4、Java 代码实现 KMP

```java
public class KMP {
    /**
     * 使用 KMP 算法得出 s 是否包含 m
     * 如果包含，返回 s 中 m 的开始索引，否则返回 -1
     */
    public static int getIndexOf(String s, String m) {
        // 首先将 str1 str2 转成字符数组
        char[] str1 = s.toCharArray();
        char[] str2 = m.toCharArray();
        int i1 = 0, i2 = 0;
        int[] next = getNextArray(str2);
        while (i1 < s.length() && i2 < m.length()) {
            if (s == null || m == null || m.length() < 1 || s.length() < m.length()) {
                return -1;
            }
            if (str1[i1] == str2[i2]) {
                // 匹配成功，同时右移
                i1++;
                i2++;
            } else if (i2 == 0) {
                // 也可以写成 next[i2] == -1，意思是一样的
                // 这个时候 i2 无论如何也无法向前移动了，只能移动 i1
                i1++;
            } else {
                // i2 还能向前移
                System.out.println(next[i2] + " <- " + i2);
                i2 = next[i2];
            }
        }

        // 如果匹配成功，那么 i2 肯定走到头了，减去 i2 就是开始位置；否则返回 -1
        return i2 == m.length() ? i1 - i2 : -1;
    }

    /**
     * 给定一个字符数组，返回一个它的 next 数组
     */
    public static int[] getNextArray(char[] ms) {
        int[] next = new int[ms.length];
        // 规定 next 数组前两个分别是 -1，0
        next[0] = -1;
        next[1] = 0;
        // cn 有两个含义：1、i - 1 位置的最长前缀值；2、cn 位置应该和i - 1 位置相比。
        int cn = 0;
        // i 是开始计算 next 数组的位置
        int i = 2;
        while (i < next.length) {
            if (ms[cn] == ms[i - 1]) {
                /**
                 * 下面三句可以简化成一句。
                 * next[i] = cn + 1;
                 * i++;
                 * cn++;
                 */
                next[i++] = ++cn;
            } else if (cn > 0) {
                // cn 位置最长匹配前后缀还有东西，还可以前移
                // i 并不增加，下次循环继续尝试新的最长匹配前缀
                cn = next[cn];
            } else {
                /**
                 * 下面两句可以简化成一句
                 * next[i] = 0;
                 * i++;
                 */
                next[i++] = 0;
            }
        }
        return next;
    }

    public static void main(String[] args) {
        String s1 = "abbstabbecabbstabbx";
        String s2 = "abbstabbx";
        System.out.println(getIndexOf(s1, s2));

    }
}
```