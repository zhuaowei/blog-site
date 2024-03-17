---
title: "LeetCode 714. 买卖股票的最佳时机含手续费"
date: 2024-03-10T16:00:09+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "观察法", "枚举优化"]
categories: ""
description: "每次交易都要扣除手续费，那我们可以在买的时候就将手续费提前扣除，当卖出股票时可以直接加上当天的股票价格。"
---

> [LeetCode 714. 买卖股票的最佳时机含手续费](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)
>
> [算法讲解082【必备】动态规划中用观察优化枚举的技巧-上](https://www.bilibili.com/video/BV1PN411j7aG/)

### 思路

每次交易都要扣除手续费，那我们可以在买的时候就将手续费提前扣除，当卖出股票时可以直接加上当天的股票价格。

### 求解过程

定义两个数组：prepare 数组表示当天必须处于持有股票状态，也就是说必须买入当天的股票或者之前的股票，然后再扣去一次手续费。done 数组表示在当天或之前完成交易的最大利润，交易可以是无数次，可以卖出当天的股票或者是之前交易的最大利润。

$$
\begin{aligned}
g(i) &= max\begin{cases}
    g(i-1), & 当天股票不参与交易\\\\
    f(i-1) + pirces_i & 卖出当天的股票

\end{cases}
\\\\
f(i) &= max\begin{cases}
    f(i-1), & 不买入当天的股票，继续持有之前的\\\\
    g(i) - pirces_i - fee & 买入当天的股票
\end{cases}
\end{aligned}
$$

**空间优化**

因为数组在更新过程中只用到了前一个，因此可以使用滚动数组进行更新，降低空间复杂度。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(1)$

### 代码

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices, int fee) {
        int prepare = -prices[0] - fee; // 购买 i 位置的股票，自己当前的余额，提前交手续费
        int done = 0; // 完成交易获得的最大利润
        int n = prices.size();
        for (int i = 1; i < n; i++) {
            // 如果卖出 i 位置的股票和不卖，取最大值
            done = max(done, prepare + prices[i]);
            // 不买入当前股票，或者交易完成后，买入当前股票，求出最大余额
            prepare = max(prepare, done - prices[i] - fee);
        }
        return done;
    }
};
```