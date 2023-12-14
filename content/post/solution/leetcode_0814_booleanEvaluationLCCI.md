---
title: "LeetCode 面试题 08.14. 布尔运算"
date: 2023-12-14T22:20:01+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "区间DP"]
categories: ""
description: "以范围内每一个运算符作为划分点，求解左右两侧结果为 true 和 false 的表达式数量，然后根据当前运算符计算出结果为 true 和 false 的数量。"
---

> [LeetCode 面试题 08.14. 布尔运算](https://leetcode.cn/problems/boolean-evaluation-lcci/)
>
> [算法讲解076【必备】区间dp-上](https://www.bilibili.com/video/BV1NQ4y1b7Uo/)

### 思路

子问题：以范围内每一个运算符作为划分点，求解左右两侧结果为 true 和 false 的表达式数量，然后根据当前运算符计算出结果为 true 和 false 的数量。

### 求解过程

递归保证 `[i, j]` 范围内的表达式是合法的，即偶数位置是数字，奇数位置是运算符，且 `i` 和 `j` 都是偶数。

**base case**

1. `i == j`：只剩下一个数字，根据数字是 `0/1`，计算出 `true` 和 `false` 的数量。

**递归过程**

分别以区间内的每一个运算符作为划分，求出两侧的 `true` 和 `false` 的数量，然后计算出本次运算 `true` 和 `false` 的数量。求出每一个字符作为划分的 `true` 和 `false` 的数量，求所有字符的到的结果的总和。

最终根据题意返回需要的 `true/false` 的数量。

### 复杂度

- 时间复杂度：$O(n^3)$
- 空间复杂度：$O(n^2)$

### 代码

```java
class Solution {
    public int countEval(String s, int result) {
        char[] str = s.toCharArray();
        int n = str.length;
        int[][][] dp = new int[n][n][];
        for (int i = 0; i < n; i += 2) {
            dp[i][i][0] = str[i] == '0' ? 1 : 0;
            dp[i][i][1] = str[i] == '1' ? 1 : 0;
        }
        int[] temp = new int[2];
        for (int i = n - 3; i >= 0; i -= 2) {
            for (int j = i + 2; j < n; j += 2) {
                for (int k = i + 1, a, b, c, d; k < j; k += 2) {
                    temp = dp[i][k - 1];
                    a = temp[0];
                    b = temp[1];
                    temp = dp[k + 1][j];
                    c = temp[0];
                    d = temp[1];
                    if (str[k] == '&') {
                        dp[i][j][1] += b * d;
                        dp[i][j][0] += a * c + a * d + b * c;
                    } else if (str[k] == '|') {
                        dp[i][j][0] += a * c;
                        dp[i][j][1] += b * c + b * d + a * d;
                    } else {
                        dp[i][j][1] += a * d + b * c;
                        dp[i][j][0] += a * c + b * d;
                    }
                }
            }
        }
        return dp[0][n - 1][result];
        // int[] res = process(s.toCharArray(), 0, s.length() - 1);
    }
    public int countEval1(String s, int result) {
        char[] str = s.toCharArray();
        int n = str.length;
        int[][][] dp = new int[n][n][];
        int[] cnt = process(str, 0, n - 1, dp);
        return cnt[result];
    }
    // [i, j] 范围内，以每个字符作为最后运算符的结果数
    public int[] process(char[] str, int i, int j, int[][][] dp) {
        if (dp[i][j] != null) {
            return dp[i][j];
        }
        int F = 0, T = 0;
        if (i == j) {
            F = str[i] == '0' ? 1 : 0;
            T = str[i] == '1' ? 1 : 0;
            dp[i][j] = new int[] {F, T};
            return dp[i][j];
        }
        int[] res = new int[2];
        for (int k = i + 1, a, b, c, d; k < j; k += 2) {
            res = process(str, i, k - 1, dp);
            a = res[0];
            b = res[1];
            res = process(str, k + 1, j, dp);
            c = res[0];
            d = res[1];
            if (str[k] == '&') {
                T += b * d;
                F += a * c + a * d + b * c;
            } else if (str[k] == '|') {
                F += a * c;
                T += b * c + b * d + a * d;
            } else {
                T += a * d + b * c;
                F += a * c + b * d;
            }
        }
        dp[i][j] = new int[] {F, T};
        return dp[i][j];
    }
}
```