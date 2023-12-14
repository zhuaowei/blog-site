---
title: "LeetCode 312. 戳气球"
date: 2023-12-14T22:18:22+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "区间DP"]
categories: ""
description: "这题需要反过来想，在区间中选择一个气球最后戳破它，这样就能保证，这个气球的左右两侧是确定的，即递归区间的两端外侧，可以计算分值，同时能够将气球分成两部分，继续求解子问题。"
---

> [LeetCode 312. 戳气球](https://leetcode.cn/problems/burst-balloons/)
>
> [算法讲解076【必备】区间dp-上](https://www.bilibili.com/video/BV1NQ4y1b7Uo/)

### 思路

如果在区间 `[i, j]` 之间选出一个气球作为第一个被戳破的，那么根据它可以将气球分为两部分，但是它左侧和右侧的气球戳破顺序是不确定的，所以无法确定本次戳破气球获得的分数。

反过来想，在 `[i, j]` 之间选择一个气球最后一个被戳破，则它的左右两侧没有被戳破的气球是确定的，就是 `i - 1` 和 `j + 1`，这个时候就可以计算出最后一个戳破的分数，同时也将气球划分出两部分。

### 求解过程

先在气球的两侧添加一个分值为 1 的气球。在 `[1, n]` 范围内求解戳破气球获取的最大分值。所以子问题是 在 `[i, j]` 范围内可以获取的最大分数。

**base case**

1. `i > j`：范围不合法，返回 0。
2. `i == j`：只有一个气球，在 [i, j] 范围内它是最后戳破的，那么肯定有 `i - 1` 和 `j + 1` 位置的气球是没有被戳破，分数为 三个位置的数相乘。

**递归过程**

设当前来到了 `[i, j]` 位置，依次选择一个气球作为最后被戳破的，计算出此次戳破和划分后的分值，求分值的最大值。

$$
\begin{equation}
f(i,j) =
\begin{cases}
0 & & {i \gt j}, \\\\
nums_{i - 1} \times nums_{j + 1} \times nums_{k} + f(i, k - 1) + f(k + 1, j) & & { i \le j 且 k = i, i + 1, ..., j}
\end{cases}
\end{equation}
$$

根据上面的递归过程分析和状态转移方程，写出记忆化搜索版本和最终的严格位置依赖版本。

base case 中的情况 2 可以放到递归过程中求解，产生的递归调用因为范围不合法，会返回 0，不影响最终的结果。所以在严格位置依赖版本中，base case省略了情况 2，同时，因为数据初始化时默认值为 0，所以也忽略掉了情况 1。

### 复杂度

- 时间复杂度：$O(n^3)$
- 空间复杂度：$O(n^2)$

### 代码

```java
class Solution {
    public int maxCoins(int[] nums) {
        int n = nums.length;
        int m = n + 2;
        int[] ball = new int[m];
        ball[0] = ball[n + 1] = 1;
        for (int i = 0; i < n; i++) {
            ball[i + 1] = nums[i];
        }
        int[][] dp = new int[m][m];
        // for (int i = 1; i <= n; i++) {
        //     dp[i][i] = ball[i - 1] * ball[i] * ball[i + 1];
        // }
        for (int i = n; i >= 1; i--) {
            for (int j = i; j <= n; j++) {
                for (int k = i; k <= j; k++) {
                    dp[i][j] = Math.max(dp[i][j], ball[i - 1] * ball[k] * ball[j + 1] + dp[i][k - 1] + dp[k + 1][j]);
                }
            }
        }
        return dp[1][n];
    }
    // 在 [i, j] 范围内选择一个气球戳破，得到的最大值是多少。
    public int process(int[] ball, int i, int j) {
        if (i > j) {
            return 0;
        }
        if (i == j) {
            return ball[i - 1] * ball[i] * ball[i + 1];
        }
        int res = 0;
        for (int k = i; k <= j; k++) {
            res = Math.max(res, ball[i - 1] * ball[k] * ball[j + 1] + process(ball, i, k - 1) + process(ball, k + 1, j));
        }
        return res;
    }
}
```