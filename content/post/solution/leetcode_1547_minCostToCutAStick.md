---
title: "LeetCode 1547. 切棍子的最小成本"
date: 2023-12-14T22:17:34+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "区间DP"]
categories: ""
description: "将切点按照大小排序，然后再在收尾添加 0 和 n，依次选择除了端点的所有切点作为下一次裁剪的点，计算出每一种切点的花费，取最小值。"
---

> [LeetCode 1547. 切棍子的最小成本](https://leetcode.cn/problems/minimum-cost-to-cut-a-stick/)
>
> [算法讲解076【必备】区间dp-上](https://www.bilibili.com/video/BV1NQ4y1b7Uo/)

### 思路

将切点按照大小排序，然后再在收尾添加 0 和 n，依次选择除了端点的所有切点作为下一次裁剪的点，计算出每一种切点的花费，取最小值。

### 求解过程

首先对切点进行排序，然后在收尾添加 0 和 n。定义子问题，在第 `[i, j]` 范围内选择一个切点，要求切点的花费最小。

**base case**

1. 当 `i > j`：没有切点，无法裁剪，返回 0。

**递归过程**

依次选择 `i, i + 1, i + 2, ..., j` 作为切点，那么无论如何选择，它的本次的花费为 `cut[j + 1] - cut[i - 1]`。

$$
\begin{equation}
f(i,j) =
\begin{cases}
0 & & {i \gt j}, \\\\
cut_{j + 1} - cut_{i - 1} + \min(f(i, k - 1), f(k + 1, j)) & & { i \le j 且 k = i, i + 1, i + 2, ..., j}
\end{cases}
\end{equation}
$$

根据上面的递归分析和状态转移方程，可以写出记忆化搜索和严格位置依赖的版本。

### 复杂度

- 时间复杂度：$O(n^3)$
- 空间复杂度：$O(n^2)$

### 代码

```java
class Solution {
    public int minCost(int n, int[] cuts) {
        int m = cuts.length;
        int[] seg = new int[m + 2];
        seg[0] = 0;
        seg[m + 1] = n;
        for (int i = 1; i <= m; i++) {
            seg[i] = cuts[i - 1];
        }
        int[][] dp = new int[m + 2][m + 2];
        Arrays.sort(seg);
        for (int i = m; i >= 1; i--) {
            for (int j = i; j <= m; j++) {
                dp[i][j] = Integer.MAX_VALUE;
                for (int k = i; k <= j; k++) {
                    dp[i][j] = Math.min(dp[i][j], dp[i][k - 1] + dp[k + 1][j]);
                }
                dp[i][j] += seg[j + 1] - seg[i - 1];
            }
        }
        return dp[1][m];
    }
    public int process(int[] seg, int i, int j, int[][] dp) {
        if (i > j) {
            return 0;
        }
        if (dp[i][j] != 0) {
            return dp[i][j];
        }
        int res = Integer.MAX_VALUE;
        for (int k = i; k <= j; k++) {
            res = Math.min(res, process(seg, i, k - 1, dp) + process(seg, k + 1, j, dp));
        }
        dp[i][j] = res + seg[j + 1] - seg[i - 1];
        return dp[i][j];
    }
}
```