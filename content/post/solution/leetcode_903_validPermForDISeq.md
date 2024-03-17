---
title: "LeetCode 903. DI 序列的有效排列"
date: 2024-03-10T19:27:34+08:00
draft: false
math: true

tags: ["LeetCode"]
categories: ""
description: "数字是什么不重要，前面已经用了哪些数字不重要，我们只需要知道，要形成上升或者下降序列，我们可以使用的数字有几个。"
---

> [LeetCode 903. DI 序列的有效排列](https://leetcode.cn/problems/valid-permutations-for-di-sequence/)
>
> [算法讲解082【必备】动态规划中用观察优化枚举的技巧-上](https://www.bilibili.com/video/BV1PN411j7aG/)

### 思路

从尝试入手，要选对尝试的思路，如果一开始使用状态表示 0 - n 的数字，哪个被用了，哪个没用，使用一个整数表示，发现题目不允许，因为 n 的范围最大是 200，无法用状态压缩DP。再仔细思考，发现数字是什么不重要，前面已经用了哪些数字不重要，我们只需要知道，要形成上升或者下降序列，我们可以使用的数字有几个。

### 求解过程

当我们来到 i 位置时，剩余的数字和上一个数字的关系是什么。也就是说我们需要知道有多少个数字是小于上一个数字，多少个数字是大于上一个数字的。又因为我们知道了位置，用了多少数字和总数字个数是知道的，所以我们只需要知道比上一个数字小的数字有多少个。

定义子问题：来到了 i 位置，如果要形成下降序列，比 i - 1 位置小的数字有 less 个，我们可以枚举每一个数字作为 i 位置的数字，然后我们就可以得出下一个子问题的比 i 位置小的数字有多少个。

同理，如果要形成上升序列，我们可以计算得到比 i - 1 位置大的数字有多少个 (n - i - less)，我们可以枚举每一个数字作为 i 位置的数字，然后得出下一个子问题的比 i 位置小的数字有多少个。

basecase: 当我们处于 0 位置，我们不知道上一个位置是上升和下降，所以我们假定上一个数字是正无穷，我们要形成一个下降序列，我们可以使用的比无穷大小的数字有 n 个。

**观察法优化**

当我们写出严格位置依赖的版本之后，它是 $n ^ 3$ 的时间复杂度。通过观察，我们发现 `dp[i][less]` 只依赖下一行的值。更细致地观察，更新第 `i` 行要么全部是上升，要么全部是下降，并且得到 `dp[i][less]` 只需要第 `i + 1` 行的左边或者右边连续的一段格子，那么我们就可以使用 `dp[i][less]` 左边和左下的格子不断进行更新，不用重复计算和。`dp[i][less]` 依赖 `dp[i + 1][0 -> less - 1]` 我们从左往右遍历，因为前一个格子已经加上过了，所以我们只需要再加上左下角的格子即可。

同理，上升序列 `dp[i][less]` 依赖 `dp[i + 1][less -> n - i - 1]`，所以我们可以倒着遍历，计算当前格子时将右边和下边的格子即可。

### 复杂度

- 时间复杂度：$O(n ^ 2)$
- 空间复杂度：$O(n ^ 2)$

### 代码

暴力递归版本

```c++
class Solution {
public:
    const int mod = 1e9 + 7;
    int numPermsDISequence(string s) {
        int n = s.size();
        return process(s, 0, n + 1, n + 1);
    }
    // 当前来到了 i 位置，比 i - 1 位置小的数字有 less 个
    int process(const string& str, int i, int less, int n) {
        int ans = 0;
        if (i == n) { // 到达尾部，方案数 + 1
            ans = 1;
        } else if (i == 0 || str[i - 1] == 'D') {
            // 要形成下降序列，0 之前假设有个无穷大
            for (int nextLess = 0; nextLess < less; nextLess++) {
                ans = (ans + process(str, i + 1, nextLess, n)) % mod;
            }
        } else {
            // 要形成上升序列
            for (int nextLess = less; nextLess < n - i; nextLess++) {
                ans = (ans + process(str, i + 1, nextLess, n)) % mod;
            }
        }
        return ans;
    }
};
```

严格位置依赖的动态规划，并且进行优化

```c++
class Solution {
public:
    const int mod = 1e9 + 7;
    int numPermsDISequence(string s) {
        int n = s.size() + 1;
        vector<vector<int>> dp(n + 1, vector<int>(n + 1));
        for (int less = 0; less <= n; less++) {
            dp[n][less] = 1;
        }
        for (int i = n - 1; i >= 0; i--) {
            // 要么上升，要么下降
            if (i == 0 || s[i - 1] == 'D') {
                dp[i][1] = dp[i + 1][0];
                for (int less = 2; less <= n; less++) {
                    // 左边加左下格子
                    dp[i][less] = (dp[i][less - 1] + dp[i + 1][less - 1]) % mod;
                }
            } else {
                dp[i][n - i - 1] = dp[i + 1][n - i - 1];
                for (int less = n -i - 2; less >= 0; less--) {
                    // 右边格子加下边格子
                    dp[i][less] = (dp[i][less + 1] + dp[i + 1][less]) % mod;
                }
            }
        }
        return dp[0][n];
    }
}
```