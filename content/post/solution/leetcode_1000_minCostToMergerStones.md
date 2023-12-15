---
title: "LeetCode 1000. 合并石头的最低成本"
date: 2023-12-15T22:03:36+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "区间DP"]
categories: ""
description: "定义子问题：在区间 `[i, j]` 时石头消除的最小花费。"
---

> [LeetCode 1000. 合并石头的最低成本](https://leetcode.cn/problems/minimum-cost-to-merge-stones/)
>
> [算法讲解077【必备】区间dp-下](https://www.bilibili.com/video/BV1du4y1L7gy/)

### 思路

定义子问题：在区间 `[i, j]` 时石头消除的最小花费。

### 求解过程

因为石头必须将 `k` 个合并成一个，且最后一次消除必须只剩下一个石头，所以在 DP 过程中是有划分依据的。在一般递归中，如果不合并，则石头可以为 `1 ... k` 个，最后一次必须合并，最终的石头必须为 k 个。根据观察，满足最后只剩下一个石头的数组，必须满足 `(n - 1) / (k - 1) == 0`。

递归过程中，需要合并某个区间的石头，无论子过程如何合并，最终合并为 1 个石头的时候，它的花费是固定的，为区间石头的和。所以我们求出前缀和数组，在递归时会用到。

**Base Case**

1. `i >= j`：无法合并，花费为 0。

**递归过程**

因为题目要求必须 k 个合成一个，要满足 `(n - 1) / (k - 1) == 0`，因此 i 的递增单位长度应为 `k - 1`，则 i 到分割点的长度一定可以合并。

分别以 `[i, j]` 区间中满足要求的点作为分割，分别计算出左右两侧合并的最小花费。在所有分割点中取最小值。最后如果此时区间长度可以进行合并，需要加上合并的花费。

### 复杂度

- 时间复杂度：$O(n^2)$
- 空间复杂度：$O(n^2)$

### 代码

```java
class Solution {
    public int mergeStones(int[] stones, int k) {
        // 反推：如果最终合并成一个，那么倒数第二次就一定是k 个
        // 倒数第三次有 n 个 k 合成，形成了 n：k - n + nk = x => k + n(k - 1) = x
        // n(k - 1) = x - k => (n + 1)(k - 1) = x - 1 => (x - 1) % (k - 1) == 0;
        int n = stones.length;
        if ((n - 1) % (k - 1) != 0) {
            return -1;
        }
        int[] pre = new int[n + 1];
        for (int i = 0; i < n; i++) {
            pre[i + 1] = pre[i] + stones[i];
        }
        int[][] dp = new int[n][n];
        for (int i = n - 2; i >= 0; i--) {
            for (int j = i + 1; j < n; j++) {
                int ans = Integer.MAX_VALUE;
                for (int m = i; m < j; m += k - 1) {
                    ans = Math.min(ans, dp[i][m] + dp[m + 1][j]);
                }
                if ((j - i) % (k - 1) == 0) {
                    ans += pre[j + 1] - pre[i];
                }
                dp[i][j] = ans;
            }
        }
        return dp[0][n - 1];
    }
    public int process(int[] pre, int i, int j, int k) {
        if (i >= j) {
            return 0;
        }
        int ans = Integer.MAX_VALUE;
        for (int start = i; start < j; start += k - 1) {
            ans = Math.min(ans, process(pre, i, start, k) + process(pre, start + 1, j, k));
        }
        if ((j - i) % (k - 1) == 0) {
            ans += pre[j + 1] - pre[i];
        }
        return ans;
    }
}
```