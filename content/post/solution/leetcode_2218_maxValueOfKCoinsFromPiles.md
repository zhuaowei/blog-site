---
title: "[LeetCode 2218. 从栈中取出 K 个硬币的最大面值和"
date: 2023-12-11T17:29:33+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "前缀和", "背包问题", "分组背包"]
categories: ""
description: "将问题转化为分组背包 DP，每一堆的硬币，因为只能从栈顶开始往下选，所以可以转化成拿 k 个硬币，得到了 x 元，每一堆选一种方案，或者不选。"
---

> [LeetCode 2218. 从栈中取出 K 个硬币的最大面值和](https://leetcode.cn/problems/maximum-value-of-k-coins-from-piles/)
>
> B站左程云：[算法讲解074【必备】背包dp-分组背包、完全背包](https://www.bilibili.com/video/BV1UM411f7YL/)

### 思路

将问题转化为分组背包 DP。每一堆硬币像栈一样取，只能从栈顶一个一个取，每一堆的方案有以下几种：选 0 个，得到 0 元；选 1 个 得到 x1 元；选 2 个，得到 x2 元 ...

将每一堆硬币视为一组，从每组中选择一个方案，最终得到在硬币数不超过 k 的情况下获得的最大价值。

### 求解过程

遍历每一个堆：
    
    1. 对每一个堆先求每一种方案的花费硬币个数和获得的钱数。
    2. 然后在剩余 0 -> m 个硬币的情况下，考虑每一种方案获得的钱数，最终求最大值。

**空间压缩**

每一个 `dp[i][j]` 依赖 `dp[i - 1][0 -> j]` 范围的值，如果不选的话 `dp[i][j] = dp[i - 1][j]`，如果只用一行来表示，`dp[j] = dp[j]`。因为 dp[j] 依赖上一行的 dp[j - 1]，所以在硬币数方面从后往前遍历。

另：因为可能出现堆中硬币数大于剩余硬币数的情况，所以不用求和到底。

### 复杂度

- 时间复杂度：$O(n \times m)$
- 空间复杂度：$O(m)$

### 代码

```java
class Solution {
    public int maxValueOfCoins(List<List<Integer>> piles, int m) {
        int n = piles.size();
        int[] dp = new int[m + 1];
        for (List<Integer> pile : piles) {
            // 求前缀和
            for (int i = 1; i < Math.min(m, pile.size()); i++) {
                pile.set(i, pile.get(i) + pile.get(i - 1));
            }
            // 计算
            for (int j = m; j >= 0; j--) {
                for (int k = 0; k < Math.min(m, pile.size()); k++) {
                    if (j >= k + 1) {
                        dp[j] = Math.max(dp[j], dp[j - k - 1] + pile.get(k));
                    }
                }
            }
        }
        return dp[m];
    }
}
```