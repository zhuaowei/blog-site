---
title: "LeetCode 494. 目标和"
date: 2023-12-10T19:10:35+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "背包问题", "01背包"]
categories: ""
description: "因为求解过程中会产生负数，不能简单地使用 dp 表进行动态规划，可以使用两层哈希表作为 dp 表，也可以使结果整体偏移 sum 求解，下面使用的是转化成 01 背包问题的解。"
---

> [LeetCode 494. 目标和](https://leetcode.cn/problems/target-sum/)
> 
> [算法讲解073【必备】背包dp-01背包、有依赖的背包](https://www.bilibili.com/list/)

### 思路

**思考一**：奇偶性，数组中的数，无论如何组合，它的奇偶性是不变的，也就是说 $\sum_{i=1}^{n}nums$ 和任意一种组合的结果的奇偶性是一样的，推出 $\sum_{i=1}^{n}nums$ 和 target 的奇偶性是一样的，如果不一样可以直接 pass。

**思考二**：每一个数字只有 `'+' / '-'` 可以选择，而且必须选择，如果全是 `'+'` 或者全是 `'-'`，结果分别为 sum 和 -sum，target 应该在这个范围内，如果不在，直接 pass。

**思考三**：数组中的数字只有两种可以选，要么是正，要么是负，这就将数组中的数分成了两类，一类是 `'+'`，一类是 `'-'`，结果分别为 sumA 和 sumB (都取正数)。

假设 $sumA - sumB = target 且 sum = sumA + sumB$。

同时加上 sum : $sumA + sumB + sumA - sumB = target + sum$

推出 : $sumA = \frac{target + sum}{2}$

由于 target 和 sum 奇偶性相同，和除以2一定可以整除。

接下来就是求在 nums 数组中找一些子序列，使他们的和等于 $\frac{target + sum}{2}$ 的个数。

### 求解过程

首先，求出 nums 数组的和，根据思考一和思考二进行剪枝。

然后求出子序列之和等于 $\frac{target + sum}{2}$ 的个数。

当前来到 (i,j) 位置，如果不选择 i 位置的数字，`cnt1 = dp[i - 1][j]`，如果选择 i 位置的数字`，cnt2 = dp[i - 1][j - nums[i]]`，求总和。

### 复杂度

- 时间复杂度：$O(N \times M)$

N 为数字个数，M 为 $\frac{(target + \sum_{i=1}^{n}nums)}{2}$

- 空间复杂度：$O(M)$

### 代码

```java
class Solution {
    public static int MAX_N = 21;
    public static int MAX_SUM = 2005;
    public static int[] dp = new int[MAX_SUM];
    public int findTargetSumWays(int[] nums, int target) {
        int sum = 0;
        for (int num : nums) {
            sum += num;
        }
        if (sum < target || ((sum & 1) ^ (target & 1)) == 1) {
            return 0;
        }
        int m = (sum + target) / 2;
        if (m < 0) {
            return 0;
        }
        Arrays.fill(dp, 0, m + 1, 0);
        dp[0] = 1;
        for (int num : nums) {
            for (int j = m; j >= num; j--) {
                dp[j] += dp[j - num];
            }
        }
        return dp[m];
    }
}
```