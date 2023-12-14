---
title: "LeetCode 1039. 多边形三角剖分的最低得分"
date: 2023-12-14T22:16:54+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "区间DP"]
categories: ""
description: "先确定三角形的两个点，然后依次选择剩余的点作为划分点，判断哪种划分方式获得的分值最小。"
---

> [LeetCode 1039. 多边形三角剖分的最低得分](https://leetcode.cn/problems/minimum-score-triangulation-of-polygon/)
>
> [算法讲解076【必备】区间dp-上](https://www.bilibili.com/video/BV1NQ4y1b7Uo/)

### 思路

先确定三角形的两个点，然后依次选择剩余的点作为划分点，判断哪种划分方式获得的分值最小。因此确定的子问题是在 `(i, j)` 中选择一个作为划分点，能获取的最小分值。

### 求解过程

首先确定数组中的首尾位置作为三角形的点，然后依次选择二者之间的点作为第三个定点，将点集划分为两个部分，之后继续在这两个部分递归，直到最终无法划分。

**base case**

1. `i + 1 >= j`：少于两个点或者范围非法，无法构成三角形，返回 0。
2. `i + 2 == j`：只剩下 3 个点，只能形成一个三角形，无法继续划分，返回计算的分值。

**递归过程**

设当前来到 `(i, j)` 位置，依次以 `i + 1, i + 2, ..., j - 1` 作为顶点划分点集，计算子集的最小分值，最终求出在所有划分中最小的分值。

$$
\begin{equation}
f(i,j) =
\begin{cases}
0 & & {i + 1 \ge j}, \\\\
nums_i \times nums_{i + 1} \times nums_{i + 2} & & { i + 2 == j}, \\\\
\min(nums_i \times nums_k \times nums_j + f(i, k) + f(k, j)) && {i + 2 \lt j 且 k = i + 1, i + 2, ..., j - 1}
\end{cases}
\end{equation}
$$

根据上述的递归过程和状态转移方程，可以写出记忆化搜索版本和严格位置依赖的 DP 版本。

### 复杂度

- 时间复杂度：$O(n^3)$
- 空间复杂度：$O(n^2)$

### 代码

```java
class Solution {
    public int minScoreTriangulation(int[] values) {
        int n = values.length;
        int[][] dp = new int[n][n];
        // for (int i = 0; i < n - 3; i++) {
        //     dp[i][i + 2] = values[i] * values[i + 1] * values[i + 2];
        // }
        for (int i = n - 3, temp; i >= 0; i--) {
            for (int j = i + 2; j < n; j++) {
                dp[i][j] = Integer.MAX_VALUE;
                for (int k = i + 1; k < j; k++) {
                    temp = values[i] * values[k] * values[j];
                    dp[i][j] = Math.min(dp[i][j], dp[i][k] + dp[k][j] + temp);
                }
            }
        }
        return dp[0][n - 1];
    }
    public int minScoreTriangulation1(int[] values) {
        int n = values.length;
        int[][] dp = new int[n][n];
        return process(values, 0, n - 1, dp);
    }
    // 将 left 和 right 连起来，剩余一个定点在 (left, right) 之间寻找
    public int process(int[] values, int i, int j, int[][] dp) {
        if (i + 1 >= j) {
            return 0;
        }
        if (dp[i][j] != 0) {
            return dp[i][j];
        }
        if (i + 2 == j) {
            dp[i][j] = values[i] * values[i + 1] * values[i + 2];
            return dp[i][j];
        }
        int res = Integer.MAX_VALUE;
        for (int k = i + 1; k < j; k++) {
            int temp = values[i] * values[k] * values[j];
            res = Math.min(res, temp + process(values, i, k, dp) + process(values, k, j, dp));
        }
        dp[i][j] = res;
        return res;
    }
}
```