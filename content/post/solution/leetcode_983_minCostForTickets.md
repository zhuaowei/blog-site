---
title: "LeetCode 83. 最低票价"
date: 2024-10-01T21:57:06+08:00
draft: false
math: true

tags: ["LeetCode", "动态规划", "每日一题"]
categories: ""
description: "2024-10-01 leetcode 每日一题。旅行计划只会影响后面的安排，不会影响前面的，所以可以倒序进行DP。"
---
> [LeetCode 83. 最低票价](https://leetcode.cn/problems/minimum-cost-for-tickets/description/)

### 思路

旅行计划只会影响后面的安排，不会影响前面的，所以可以倒序进行DP。

### 求解过程

设 $dp_i$ 是从 $days_i$ 开始旅行的最低票价，那么从 $dp_j$ 即是从 $days_j$ 开始旅行的最低票价 $(j < i)$。那么

$$
\begin{equation}
\begin{aligned}
    dp(j) = \min(dp(i) + costs_i)
\end{aligned}
\end{equation}
$$

根据题目规则和贪心，i 和 j 有如下关系：

1. 从 j 出发的旅行 和 从 i 出发的旅行必须连续。也就是说 从 j 出发的票的有效期必须包含 i - 1。
2. 从 j 出发到达 i，最好刚好到过了有效期花费才最低。

### 复杂度

每个日期最多会遍历一次，并且每种票都会尝试一次。所以时间复杂度为 $O(n)$

使用了一个与日期相等长度的数组存储中间值，所以空间复杂度为 $O(n)$

### 代码

```c++
class Solution {
public:
    int mincostTickets(vector<int>& days, vector<int>& costs) {
        vector<int> tickets{ 1, 7, 30 };
        int n = days.size();
        vector<int> dp(n, INT_MAX);

        function<int(int)> dfs = [&](int i) {
            if (i == n) {
                return 0;
            }
            if (dp[i] != INT_MAX) {
                return dp[i];
            }
            int ans = INT_MAX;
            for (int k = 0; k < costs.size(); k++) {
                int right = i;
                while (right < days.size() && days[right] <= days[i] + tickets[k] - 1) {
                    right++;
                }
                ans = min(ans, costs[k] + dfs(right));
            }
            dp[i] = ans;
            return ans;
        };
        return dfs(0);

    }
};
```
