---
title: "LeetCode 123. 买卖股票的最佳时机 III"
date: 2024-01-20T22:19:49+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "观察法", "枚举优化"]
categories: ""
description: "将两次股票交易分开来处理，遍历以每一天作为第二次股票卖出时得到的利润，然后的出最大利润。"
---

> [LeetCode 123. 买卖股票的最佳时机 III](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iii/)
>
> [算法讲解082【必备】动态规划中用观察优化枚举的技巧-上](https://www.bilibili.com/video/BV1PN411j7aG/)

### 思路

将两次股票交易分开来处理，遍历以每一天作为第二次股票卖出时得到的利润，然后的出最大利润。

### 求解过程

使用一个 `dp1` 数组，记录 `0 - i` 范围内，交易一次股票所获得的最大利润，买入和卖出可以在 `0 - i` 的任意一天。

使用一个 `dp2` 数组，记录下 `0 - i` 范围内，交易两次股票所获得的最大利润，卖出第二次股票必须在第 `i` 天。

遍历每一天，作为第二次股票卖出的时间 `j`，然后遍历在开始和卖出股票的之间的每一天，作为第二次股票买入的时间 `i`，在这之间可以进行第一次股票交易。那么两次股票交易的总利润就等于 $dp2[j] = max(dp1[i] + prices[j] - prices[i])$ ，我们把含有 `j` 的提取出来就是 $dp2[j] = max(dp1[i] - prices[i]) + prices[j]$，而 `dp1[i] - prices[i])` 我们不用每次遍历，可以使用一个数组 `best` 存储起来。

因为 dp1 dp2 best 数组只用到了它的前一个位置的结果，所以我们可以使用滚动数组更新它，优化空间复杂度。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(1)$

### 代码

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        int dp1 = 0; // 0 - i 范围内交易一次获取的最大利润
        int dp2 = 0; // 0 - i 范围内交易两次，且最后一次卖出交易为 i 的最大利润
        int best; // best[i] 0 - i 范围内 dp1[i] - prices[i] 的最大值
        best = -prices[0]; // dp1[0] - prices[0] == -prices[0];
        int ans = 0;
        int minStock = prices[0];
        for (int i = 1; i < n; i++) { // 0 天最大利润为 0
            minStock = min(minStock, prices[i]);
            dp1 = max(dp1, prices[i] - minStock);
            best = max(best, dp1 - prices[i]);
            dp2 = best + prices[i];
            ans = max(ans, dp2);
        }
        return ans;
    }
};
```

没有经过时间和空间优化的版本，就是最初的版本，无法通过全部用例，但是后面的优化基于此版本。

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        vector<int> dp1(n); // 0 - i 范围内交易一次获取的最大利润
        vector<int> dp2(n); // 0 - i 范围内交易两次，且最后一次交易为 i 的最大利润

        int minStock = prices[0];
        for (int i = 1; i < n; i++) { // 0 天最大利润为 0
            minStock = min(minStock, prices[i]);
            dp1[i] = max(dp1[i - 1], prices[i] - minStock);
        }
        int ans = 0;
        for (int j = 1; j < n; j++) { // 0 天最大利润为 0
            // 遍历每一个以 0 - i 范围内交易一次，i 第二次买入的最大利润
            for (int i = 0; i <= j; i++) {
                dp2[j] = max(dp2[j], dp1[i] + prices[j] - prices[i]);
            }
            ans = max(ans, dp2[j]);
        }
        return ans;
    }
};
```