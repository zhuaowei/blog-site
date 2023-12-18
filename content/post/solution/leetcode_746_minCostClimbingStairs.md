---
title: "LeetCode 746. 使用最小花费爬楼梯"
date: 2023-12-17T20:06:16+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "每日一题"]
categories: ""
description: "2023-12-17 leetcode 每日一题，当我们来到某一个阶梯时，我们肯定需要跳出这个台阶，也就是说这个台阶的花费是一定会用掉的。因此，我们可以这样想，我们来到某个台阶，先支付费用，我们下次跳出台阶就可以免费。"
---

> [LeetCode 746. 使用最小花费爬楼梯](https://leetcode.cn/problems/min-cost-climbing-stairs/)

### 思路

从楼梯的第一阶或者第二阶出发，计算出走完整个台阶的最小花费。当我们来到某一个阶梯时，我们肯定需要跳出这个台阶，也就是说这个台阶的花费是一定会用掉的。因此，我们可以这样想，我们来到某个台阶，先支付费用，我们下次跳出台阶就可以免费。

我们来到某个台阶时，它肯定从这个台阶的前一或者前二台阶跳过来。我们选择花费较小的方案。

### 求解过程

1. 选择起点，我们可以选择第 `0 / 1` 台阶开始，当我们到达第 `0` 个台阶时，花费为 `cost[0]`；当我们来到第 `1` 个台阶时，花费为 `cost[1]`。
2. 求出来到第 `i` 个台阶时的最小花费。
3. 当全部的台阶都计算完成后，我们可以从倒数第一个台阶跳出，也可以从倒数第二个台阶跳出，取最小花费的方案。

**状态转移方程**

$$
\begin{equation}
\begin{aligned}
    f(i) = cost_i + \min(f(i - 1), f(i - 2))
\end{aligned}
\end{equation}
$$

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(1)$

### 代码

```java
class Solution {
    public int minCostClimbingStairs(int[] cost) {
        int n = cost.length;
        int prev = cost[0];
        int cur = cost[1];
        int next;
        for (int i = 2; i < n; i++) {
            next = cost[i] + Math.min(prev, cur);
            prev = cur;
            cur = next;
        }
        return Math.min(prev, cur);
    }
}
```