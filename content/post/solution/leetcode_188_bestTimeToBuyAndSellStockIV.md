---
title: "LeetCode 188. 买卖股票的最佳时机 IV"
date: 2024-01-20T22:20:04+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "观察法", "枚举优化"]
categories: ""
description: "根据前面几题的经验，使用一个二维 dp 表，记录下交易次数和范围的最大利润，遍历所有可能，得出交易 k 次，在整个数组范围内的最大利润。"
---

> [LeetCode 188. 买卖股票的最佳时机 IV](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iv/)
>
> [算法讲解082【必备】动态规划中用观察优化枚举的技巧-上](https://www.bilibili.com/video/BV1PN411j7aG/)

### 思路

根据前面几题的经验，使用一个二维 dp 表，记录下交易次数和范围的最大利润，遍历所有可能，得出交易 k 次，在整个数组范围内的最大利润。

### 求解过程

当交易次数达到一定数量时，相当于无线次交易，可以使用之前的方式，抓取每一个上坡，得到最大利润。否则：

定义一个 dp 表： `dp[i][j]` 表示交易 i 次，在 `0 - j` 范围上的最大利润，当我们来到第 `i` 次交易，范围 `0 - j` 时，如果最后一次交易没有发生在第 `j` 天，则 `dp[i][j] = dp[i][j - 1]`；如果最后一次交易发生在第 `j` 天，则倒数第二次交易可能发生在 `0 - j` 之间的每一天，所以遍历 `0 - j` ，找出利润最大的交易方案。

$$
\begin{equation}
\begin{aligned}
f(i,j) = \begin{cases}
    f(i, j - 1)\\\\
    \max(f(i-1, p) + prices_p) + prices_j
\end{cases}
\end{aligned}
\end{equation}
$$

$\max(f(i-1, p) + prices_p)$ 可以提前处理得到，不用到时候全部遍历一遍。

### 复杂度

- 时间复杂度：$O(n \times k)$
- 空间复杂度：$O(n \times k)$

空间复杂度可以优化成 $O(n)$，分析循环的依赖关系。

### 代码

```c++
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        int n = prices.size();
        if (k >= n / 2) {
            return getMaxProfitWithoutLimit(prices);
        }
        // dp[i][j] 在 0 - j 范围内，交易 i 次的最大利润
        vector<vector<int>> dp(k + 1, vector<int>(n));
        // dp[...][0] 第 0 天，利润为 0
        // dp[0][...] 交易 0 次，利润为 0
        for (int i = 1; i <= k; i++) {
            int best = -prices[0];
            for (int j = 1; j < n; j++) {
                // dp[i][j] = max(dp[i][j - 1], dp[i - 1][0] + prices[j] - prices[0]);
                dp[i][j] = max(dp[i][j - 1], best + prices[j]);
                best = max(best, dp[i - 1][j] - prices[j]);
            }
        }
        return dp[k][n - 1];
    }
    int getMaxProfitWithoutLimit(vector<int>& prices) {
        int n = prices.size();
        int ans = 0;
        for (int i = 1; i < n; i++) {
            if (prices[i] - prices[i - 1] > 0) {
                ans += prices[i] - prices[i - 1];
            }
        }
        return ans;
    }
};
```

无枚举优化的版本，暴力搜索 i 次交易，0 - j 范围内的最大利润。

```c++
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        int n = prices.size();
        if (k >= n / 2) {
            return getMaxProfitWithoutLimit(prices);
        }
        // dp[i][j] 在 0 - j 范围内，交易 i 次的最大利润
        vector<vector<int>> dp(k + 1, vector<int>(n));
        // dp[...][0] 第 0 天，利润为 0
        // dp[0][...] 交易 0 次，利润为 0
        for (int i = 1; i <= k; i++) {
            for (int j = 1; j < n; j++) {
                dp[i][j] = max(dp[i][j - 1], dp[i - 1][0] + prices[j] - prices[0]);
                for (int p = 0; p < j; p++) {
                    dp[i][j] = max(dp[i][j], dp[i - 1][p] + prices[j] - prices[p]);
                }
            }
        }
        return dp[k][n - 1];
    }
    int getMaxProfitWithoutLimit(vector<int>& prices) {
        int n = prices.size();
        int ans = 0;
        for (int i = 1; i < n; i++) {
            if (prices[i] - prices[i - 1] > 0) {
                ans += prices[i] - prices[i - 1];
            }
        }
        return ans;
    }
};
```