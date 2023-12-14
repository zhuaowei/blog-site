---
title: "LeetCode 486. 预测赢家"
date: 2023-12-14T22:15:28+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "区间DP"]
categories: ""
description: "求解子问题：在区间 [i, j] 上，如果 A 先手，A 可以获取的最大分值是多少。"
---

> [LeetCode 486. 预测赢家](https://leetcode.cn/problems/predict-the-winner/)
>
> [算法讲解076【必备】区间dp-上](https://www.bilibili.com/video/BV1NQ4y1b7Uo/)

### 思路

求解子问题：在区间 `[i, j]` 上，如果 A 先手，A 可以获取的最大分值是多少。

### 求解过程

先通过递归求解。如果当前的区间是 `[i, j]`：

**base case**

1. `i == j`：只有一个数，A 只能选 nums[i]。
2. `i + 1 == j`：有两个数，A 先手，A 肯定选较大的。

**递归过程**

1. 如果 A 选择了 `nums[i]`：在 `[i + 1, j]` 上 B 就是先手的，如果此时 B 选，他肯定会选择有利于自己的结果，让 A 在剩下的区间获得的分值最小。即 $min(f(i + 2, j), f(i + 1, j - 1))$。
2. 同理，如果 A 选择了 `nums[j]`，B 同样会让 A 在剩余区间获得最小分值。即 $min(f(i + 1, j - 1), f(i, j - 2))$。

经过上方的思考，A 决定在两种选择中选择较大值。

$$
\begin{equation}
f(i,j) = max \left\{
\begin{aligned}
nums_i + min(f(i + 2, j), f(i + 1, j - 1)) \\\\
nums_j + min(f(i + 1, j - 1), f(i, j - 2))
\end{aligned}
\right.
\end{equation}
$$

挂个缓存表或者修改为严格位置依赖的版本都可以过。

### 复杂度

- 时间复杂度：$O(n^2)$
- 空间复杂度：$O(n^2)$

### 代码

```java
class Solution {
    public boolean predictTheWinner(int[] nums) {
        int n = nums.length;
        int[][] dp = new int[n][n];
        int sum = 0;
        for (int i = 0; i < n; i++) {
            dp[i][i] = nums[i];
            sum += nums[i];
        }
        for (int i = 0; i < n - 1; i++) {
            dp[i][i + 1] = Math.max(nums[i], nums[i + 1]);
        }
        for (int i = n - 3; i >= 0; i--) {
            for (int j = i + 2; j < n; j++) {
                dp[i][j] = Math.max(
                    nums[i] + Math.min(dp[i + 2][j], dp[i + 1][j - 1]),
                    nums[j] + Math.min(dp[i][j - 2], dp[i + 1][j - 1])
                );
            }
        }
        return dp[0][n - 1] >= (sum - dp[0][n - 1]);
    }
    public boolean compute1(int[] nums) {
        int sum = 0;
        for (int num : nums) {
            sum += num;
        }
        int res = process(nums, 0, nums.length - 1);
        return res >= (sum - res);
    }
    // 返回在 [left, right] 上，A 先手的情况下，可以获得的最大分数
    public int process(int[] nums, int left, int right) {
        if (left == right) {
            return nums[left];
        }
        if (left + 1 == right) {
            return Math.max(nums[left], nums[right]);
        }
        // 如果 A 选作左边的，那么 B 可以选左右两边，B 选完后肯定留下较小的给 A。
        int res1 = nums[left] + Math.min(process(nums, left + 2, right), process(nums, left + 1, right - 1));
        int res2 = nums[right] + Math.min(process(nums, left, right - 2), process(nums, left + 1, right - 1));
        return Math.max(res1, res2);
    }
}
```