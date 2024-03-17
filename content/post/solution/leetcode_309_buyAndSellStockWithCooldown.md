---
title: "LeetCode 309. 买卖股票的最佳时机含冷冻期"
date: 2024-03-10T16:06:20+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "观察法", "枚举优化"]
categories: ""
description: ""
---

> [LeetCode 309. 买卖股票的最佳时机含冷冻期](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-cooldown/)
>
> [算法讲解082【必备】动态规划中用观察优化枚举的技巧-上](https://www.bilibili.com/video/BV1PN411j7aG/)

### 思路

跟带手续费的股票问题一样，使用一个数组表示已经完成冷静期的持有股票的状态，一个数组表示交易完成的状态。

### 求解过程

使用两个数组：prepare 数组表示持有股票状态的最大当前余额，除此之外，还需要包含冷静期，也就是来到当前天时，已经过完冷静期了。done 数组表示交易完成的状态，可以完成无数次交易。

$$
\begin{aligned}
g(i) &= max\begin{cases}
    g(i-1), & 当天股票不参与交易\\\\
    f(i-1) + pirces_i & 卖出当天的股票

\end{cases}
\\\\
f(i) &= max\begin{cases}
    f(i-1), & 不买入当天的股票，继续持有之前的\\\\
    g(i-2) - pirces_i & 买入当天的股票，跳过之前的冷静期
\end{cases}
\end{aligned}
$$

`prepare[0]` 和 `prepaer[1]` 表示第一次购买，如果第一天就完成了买卖交易，再加上冷静期，需要第3天才能买，所以不考虑。如果前两天必须要买一次，那么肯定买股价低的。

前两天完成交易只能是第0天买第1天卖，所以收益最大为 0 或者 `prices[1] - prices[0]`

因为有冷静期，更新 prepare 数组需要用到前 2 天的交易完成数据，所以在空间优化时需要额外注意一下，先用一个临时变量存储一下，防止被覆盖。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(1)$

### 代码

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        if (n < 2) {
            return 0;
        }
        // 完成一次购买，但是要扣掉冷静期的余额
        int prepare = max(-prices[0], -prices[1]);
        int lastDone = 0; // 完成交易的最大利润
        int curDone = max(0, prices[1] - prices[0]);
        for (int i = 2, temp; i < n; i++) {
            temp = curDone;
            curDone = max(curDone, prepare + prices[i]);
            prepare = max(prepare, lastDone - prices[i]);
            lastDone = temp;
        }
        return curDone;
    }
};
```