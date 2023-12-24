---
title: "LeetCode 1954. 收集足够苹果的最小花园周长"
date: 2023-12-24T20:50:40+08:00
draft: false
math: true

tags: ["LeetCode", "二分", "", "每日一题"]
categories: ""
description: "根据题意可以得出花园的周长跟容纳苹果的最大数量是有关系的，通过归纳得出苹果数量与花园边长的关系式，然后进行二分求解。"
---

> [LeetCode 1954. 收集足够苹果的最小花园周长](https://leetcode.cn/problems/minimum-garden-perimeter-to-collect-enough-apples/)

### 思路

根据题意可以得出花园的周长跟容纳苹果的最大数量是有关系的，通过归纳得出苹果数量与花园边长的关系式，然后进行二分求解。

### 求解过程

**边长与苹果的关系**

有题意可知，花园的边长是偶数，放置的苹果可以被均分为四部分。如果设置边长为 2x ，则每一部分的苹果分布与 x 的关系如下：

```text
1   2   3   ... x + 1
2   3   4   ... x + 2
...
x   x + 1   x + 2   ... 2x
```
所以可以归纳出苹果的数量为：

$$
\begin{equation}
\begin{aligned}
f(x) &= 4 \times x \times \frac{(\frac{x + 1}{2} + \frac{3x + 1}{2}) \times (x + 1)}{2}\\\\
&= 4 \times x \times \frac{(2x + 1) \times (x + 1)}{2}\\\\
&= 2x \times (2x + 1) \times (x + 1)
\end{aligned}
\end{equation}
$$

**二分答案法**

根据上述的公式，可以得知 $f(x) \approx x^3$，而苹果数量最大为 $10^{15}$，所以 x 的范围应该是 $[0, 10^5]$。

根据二分答案法，求出 x 为 mid 时的最大苹果数量。如果可以放下，记录下答案，右边界左移；，否则，左边界右移。最终边长的长度为 $8x$。

### 复杂度

- 时间复杂度：$O(log_2n)$
- 空间复杂度：$O(1)$

### 代码

```java
class Solution {
    public long minimumPerimeter(long neededApples) {
        // 设边长为 2x，则最大可以放置的苹果数为 (2 * x + 1) * (x + 1) * x * 2
        int left = 0, right = 100000, ans = 0, mid;
        while (left <= right) {
            mid = left + (right - left) / 2;
            if (getMaxApples((long)mid) >= neededApples) {
                ans = mid;
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return ans * 8;
    }
    public long getMaxApples(long x) {
        return (2 * x + 1) * (x + 1) * x * 2;
    }
}
```