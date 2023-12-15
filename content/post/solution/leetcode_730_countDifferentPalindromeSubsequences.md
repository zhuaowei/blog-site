---
title: "LeetCode 730. 统计不同回文子序列"
date: 2023-12-15T22:04:42+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "区间DP"]
categories: ""
description: "求 DP 的子问题，在区间 [i, j] 中的回文子序列数量。需要根据区间两端的字符和区间内的与端点相同的字符数目分类讨论。"
---

> [LeetCode 730. 统计不同回文子序列](https://leetcode.cn/problems/count-different-palindromic-subsequences/)
>
> [算法讲解077【必备】区间dp-下](https://www.bilibili.com/video/BV1du4y1L7gy/)

### 思路

定义子问题：在区间 `[i, j]` 的回文子序列的数目。

### 求解过程

**Base Case**

1. `i > j`：无法构成子序列，返回 0。
2. `i == j`：只有一个字符，满足回文子序列，返回 1。

**递归过程**

1. 如果左右两侧字符不相等：分别求出区间 `[i, j - 1]` 和区间 `[i + 1, j]` 的回文子序列数量，因为在区间 `[i + 1, j - 1]` 有重复计算的回文子序列，所以要减掉。
2. 如果左右两侧字符相等，需要分类讨论：

    a. 如果区间中不存在与端点相同的字符：`2 * dp[i + 1, j - 1] + 2`，乘 2 分别是加上端点的和不加端点的，2 是只有端点形成的长度为 1 和 2 的回文子序列。

    b. 如果区间中存在一个与端点相同的字符：`2 * dp[i + 1, j - 1] + 1`，跟上面类似，+1 是区间内部已经有长度为 1 的端点字符的回文子序列，不需要再加了。

    c. 如果区间中存在两个及以上与端点相同的字符：只关心与端点最近的两个字符 L R，上述长度为 1 和 2 的与端点相同字符的回文子序列就没有了，因为内部区间已经包含了，但是会有重复的。因为 `[L + 1, R - 1]` 区间的回文因为 L R 会计算一次，在 `[i, j]` 又会计算一次，所以要减去 `dp[L + 1][R - 1]`。

为了快速求出区间端点内部与之相同的最近的字符位置，我们使用两个数组记录下与他们最近的左右两侧相同字符的位置，这样在递归中可以快速查找。

### 复杂度

- 时间复杂度：$O(n^2)$
- 空间复杂度：$O(n^2)$

### 代码

```java
class Solution {
    int mod = 1000000007;
    public int countPalindromicSubsequences(String s) {
        char[] str = s.toCharArray();
        int n = str.length;
        int[] left = new int[n];
        int[] right = new int[n];
        int[] last = new int[4];
        long[][] dp = new long[n][n];
        Arrays.fill(last, -1);
        for (int i = 0; i < n; i++) {
            dp[i][i] = 1;
            left[i] = last[str[i] - 'a'];
            last[str[i] - 'a'] = i;
        }
        Arrays.fill(last, n);
        for (int i = n - 1; i >= 0; i--) {
            right[i] = last[str[i] - 'a'];
            last[str[i] - 'a'] = i;
        }
        for (int i = n - 2; i >= 0; i--) {
            for (int j = i + 1; j < n; j++) {
                long res = 0;
                if (str[i] != str[j]) {
                    res = dp[i][j - 1] + dp[i + 1][j] - dp[i + 1][j - 1] + mod;
                } else {
                    int L = right[i];
                    int R = left[j];
                    if (L > R) {
                        res = dp[i + 1][j - 1] * 2 + 2;
                    } else if (L == R) {
                        res = dp[i + 1][j - 1] * 2 + 1;
                    } else {
                        res = dp[i + 1][j - 1] * 2 - dp[L + 1][R - 1] + mod;
                    }
                }
                dp[i][j] = res % mod;
            }
        }
        return (int)dp[0][n - 1];
    }
    public long process(char[] str, int[] left, int[] right, int i, int j, long[][] dp) {
        if (i > j) {
            return 0;
        }
        if (i == j) {
            return 1;
        }
        if (dp[i][j] != 0) {
            return dp[i][j];
        }
        long res = 0;
        if (str[i] != str[j]) {
            res = process(str, left, right, i, j - 1, dp) + process(str, left, right, i + 1, j, dp) - process(str, left, right, i + 1, j - 1, dp) + mod;
        } else {
            int L = right[i];
            int R = left[j];
            if (L > R) {
                res = process(str, left, right, i + 1, j - 1, dp) * 2 + 2;
            } else if (L == R) {
                res = process(str, left, right, i + 1, j - 1, dp) * 2 + 1;
            } else {
                res = process(str, left, right, i + 1, j - 1, dp) * 2 - process(str, left, right, L + 1, R - 1, dp) + mod;
            }
        }
        res %= mod;
        dp[i][j] = res;
        return res;
    }
}
```