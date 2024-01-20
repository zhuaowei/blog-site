---
title: "LeetCode 122. 买卖股票的最佳时机 II"
date: 2024-01-20T22:19:41+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "观察法", "枚举优化"]
categories: ""
description: "因为可以交易无限次，我们可以抓住每次股价上升的趋势，赚取所有的利润。一个连续上升的利润等同于拆分后每段的利润之和，所以我们只需要跟前一个位置的股价进行比较，如果利润为正就赚，否则赚 0。"
---

> [LeetCode 122. 买卖股票的最佳时机 II](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/)
>
> [算法讲解082【必备】动态规划中用观察优化枚举的技巧-上](https://www.bilibili.com/video/BV1PN411j7aG/)

### 思路

因为可以交易无限次，我们可以抓住每次股价上升的趋势，赚取所有的利润。一个连续上升的利润等同于拆分后每段的利润之和，所以我们只需要跟前一个位置的股价进行比较，如果利润为正就赚，否则赚 0。

### 求解过程

遍历股价数组，将当前值与前一值比较，如果当前值较大，总利润增加，否则，总利润不变。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(1)$

### 代码

```java
class Solution {
    public int maxProfit(int[] prices) {
        int ans = 0;
        int n = prices.length;
        for (int i = 1; i < n; i++) {
            ans += Math.max(0, prices[i] - prices[i - 1]);
        }
        return ans;
    }
}
```