---
title: "LeetCode 1235. 规划兼职工作"
date: 2024-03-17T22:31:56+08:00
draft: false
math: true

tags: ["LeetCode"]
categories: ""
description: "按照工作的结束时间进行排序，然后分别找出在前 n 个工作可以选择的情况下的最大利润，最后在所有利润中找到最大利润。"
---

> [LeetCode 1235. 规划兼职工作](https://leetcode.cn/problems/maximum-profit-in-job-scheduling/)
>
> [算法讲解083【必备】动态规划中用观察优化枚举的技巧-下](https://www.bilibili.com/video/BV1pN41157uX/)

### 思路

按照工作的结束时间进行排序，然后分别找出在前 n 个工作可以选择的情况下的最大利润，最后在所有利润中找到最大利润。

### 求解过程

1. 首先将所有数据组合成一个二维数组，然后按照结束时间进行排序。
2. 定义一个 `dp` 表，`dp[i]` 表示在前 `i` 个工作中选择，可以获得的最大利润。
3. 定义转移方程，如果我一定要做第 `i` 个工作，那么我获得第 `i` 份工作的利润，并且只能选择第 `i` 份工作开始时间之前结束的工作，求利润之和；如果我不选择第 `i` 份工作，那么我获得的利润就是前 `i - 1` 份工作之前可以赚取的最大值。

$$
\begin{equation}
\begin{aligned}
f(i) = \begin{cases}
    f(i - 1) \\\\
    profit_i + f(j)
\end{cases}\\\\
\end{aligned}
\end{equation}
$$

$j$ 为结束时间小于等于 $startTime_i$ 的工作的最右侧索引，因为结束时间是有序的，所以可以使用二分查找快速查询。如果找不到，说明没有工作可以选择，之前的利润为 0。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(n)$

### 代码

```c++
class Solution {
public:
    int jobScheduling(vector<int>& startTime, vector<int>& endTime, vector<int>& profit) {
        int len = startTime.size();
        vector<vector<int>> jobs(len, vector<int>(3));
        for (int i = 0; i < len; i++) {
            jobs[i][0] = startTime[i];
            jobs[i][1] = endTime[i];
            jobs[i][2] = profit[i];
        }
        sort(jobs.begin(), jobs.end(), [&](vector<int>& a, vector<int>& b) {
            return a[1] < b[1];
        });
        vector<int> dp(len);
        // 只有一个工作可以选择
        dp[0] = jobs[0][2];
        for (int i = 1, pos, pro; i < len; i++) {
            // 按照当前工作的开始时间进行查找
            pos = find(jobs, jobs[i][0]);
            // 之前的利润
            pro = pos == -1 ? 0 : dp[pos];
            // 当前工作的利润
            pro += jobs[i][2];
            // 做和不做取最大值
            dp[i] = max(dp[i - 1], pro);
        }
        
        return dp[len - 1];
    }
    /* 递归版本：在前 i 个任务中选择，利润最大是多少 */
    int f(int i, vector<vector<int>>& jobs, vector<int>& dp) {
        if (i < 0) {
            return 0;
        }
        if (i == 0) {
            return jobs[0][2];
        }
        if (dp[i] != -1) {
            return dp[i];
        }
        int startTime = jobs[i][0];
        int endTime = jobs[i][1];
        int profit = jobs[i][2];
        int ans = f(i - 1, jobs, dp);
        int pos = find(jobs, startTime);
        ans = max(ans, profit + f(pos, jobs, dp));
        dp[i] = ans;
        return ans;
    }
    // 按照结束时间查找，找到 <= endTime 的最大索引
    int find(vector<vector<int>>& jobs, int target) {
        int left = 0, right = jobs.size() - 1, mid, ans = -1;
        while (left <= right) {
            mid = left + (right - left) / 2;
            if (jobs[mid][1] <= target) {
                ans = mid;
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return ans;
    }
}
```