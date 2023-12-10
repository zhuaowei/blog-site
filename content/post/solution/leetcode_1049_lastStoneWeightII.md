---
title: "LeetCode 1049. 最后一块石头的重量 II"
date: 2023-12-10T19:26:20+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "背包问题", "01背包问题"]
categories: ""
description: "将一组石头分成两组，分别捏成两个大石头，则抵消后的结果为较大的减去较小的那块。如果要让结果尽量小，则需要让两堆石头的重量尽量接近。"
---

> [LeetCode 1049. 最后一块石头的重量 II](https://leetcode.cn/problems/last-stone-weight-ii/)
>
> [算法讲解073【必备】背包dp-01背包、有依赖的背包](https://www.bilibili.com/list/)

### 思路

两块石头碰撞会相互抵消，最终留下较大的减去较小的那块。

如果将一组石头分成两组，分别捏成两个大石头，则抵消后的结果为**较大的减去较小的那块**。如果要让结果尽量小，则需要让两堆石头的重量尽量接近，甚至相等。

### 求解过程

先求出一组石头的总重量 sum，取中间值 $\frac{sum}{2}$，让其中一堆石头的重量尽量接近 $\frac{sum}{2}$，求出这一堆石头的重量后，另外一堆的重量也就知道了，最终抵消的结果也就得出来了。

求在数组中，石头重量 $\le \frac{sum}{2}$ 的最大重量，花费和价值相同的01背包问题，假设结果为 `w1`，那么 `w2 = sum - w1`，推出 `w1 <= w2`，最终抵消的结果为 `sum - w1 - w1`。

### 复杂度

- 时间复杂度：$O(N \times M)$

N 为石头数量，M 为 $\frac{\sum_{i=1}^{n}nums}{2}$

- 空间复杂度：$O(M)$

### 代码

```java
class Solution {
    public int lastStoneWeightII(int[] stones) {
        int n = stones.length, sum = 0;
        for (int stone : stones) {
            sum += stone;
        }
        int m = sum / 2;
        int[] dp = new int[m + 1];
        for (int stone : stones) {
            for (int j = m; j >= stone; j--) {
                dp[j] = Math.max(dp[j], dp[j - stone] + stone);
            }
        }
        return sum - dp[m] - dp[m];
    }
}
```