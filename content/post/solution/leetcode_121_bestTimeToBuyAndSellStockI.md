---
title: "LeetCode 121. 买卖股票的最佳时机"
date: 2024-01-20T22:18:52+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "观察法", "枚举优化"]
categories: ""
description: "根据题意，买卖不能再同一天，但是它又说，如果不能获得利润就返回 0。所以我们就可以理解为：买卖可以在同一天，返回交易一次的最大利润，如果不能获得利润，那么最大利润肯定为 0。所以我们求出每个位置减去至今位置遇到的最小值，得到的结果取最大就是最大利润。"
---

> [LeetCode 121. 买卖股票的最佳时机](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/)
>
> [算法讲解082【必备】动态规划中用观察优化枚举的技巧-上](https://www.bilibili.com/video/BV1PN411j7aG/)

### 思路

根据题意，买卖不能再同一天，但是它又说，如果不能获得利润就返回 0。所以我们就可以理解为：买卖可以在同一天，返回交易一次的最大利润，如果不能获得利润，那么最大利润肯定为 0。所以我们求出每个位置减去至今位置遇到的最小值，得到的结果取最大就是最大利润。

### 求解过程

使用一个变量记录至今遇到的最小值，每来到新的一天，用当前的股票价值计算出当前位置可能获得的最大利润，然后再与全局最大利润相比，更新全局最大利润。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(1)$

### 代码

```java
class Solution {
    public int maxProfit(int[] prices) {
        int mi = prices[0];
        int ans = 0;
        for (int p : prices) {
            ans = Math.max(ans, p - mi);
            mi = Math.min(mi, p);
        }
        return ans;
    }
}
```