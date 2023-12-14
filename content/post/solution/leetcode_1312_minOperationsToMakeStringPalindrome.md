---
title: "LeetCode 1312. 让字符串成为回文串的最少插入次数"
date: 2023-12-14T17:34:11+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "区间DP"]
categories: ""
description: "提取子问题，求使 [i, j] 范围内的字符串变成回文串的最小操作是次数，根据两侧字符是否相等决定递归子问题的范围。先求出递归方式解法，然后修改成记忆化搜索、严格位置依赖和空间压缩版本。"
---

> [LeetCode 1312. 让字符串成为回文串的最少插入次数](https://leetcode.cn/problems/minimum-insertion-steps-to-make-a-string-palindrome/)
>
> [算法讲解076【必备】区间dp-上](https://www.bilibili.com/video/BV1NQ4y1b7Uo/)

### 思路

提取子问题，求使 `[i, j]` 范围内的字符串变成回文串的最小操作是次数，根据两侧字符是否相等决定递归子问题的范围。

### 求解过程

先写出递归方式。按照上述的思路，求使子串变成回文的最小操作次数。

**base case**

1. `i == j`：只有一个字符，肯定是回文，返回 0
2. `i + 1 == j`：只有两个字符，如果相等是回文，返回 0；否则返回 1，添加一个字符。

**递归过程**

1. `str[i] == str[j]`：两侧已经构成回文，递归调用 `process(i + 1, j - 1)`。
2. 否则，添加一个字符，分别使左右两侧字符形成回文，再递归调用下一个范围 `process(i + 1, j)` 和 `process(i, j - 1)`, 选择最小的操作次数，再加上本次递归的一次操作次数。

**空间优化**

分析递归调用的过程，有重复调用递归，所以挂一个 DP 表减少展开次数。然后修改为严格位置依赖的版本，根据分析，每个 `(i, j)` 位置的格子只依赖左侧，下侧和左下角的位置，因此定义 DP 顺序为 `i` 从大到小，`j` 从左向右。使用一维表格时，为了防止当前位置覆盖上一行的值，使用一个变量临时存储。

### 复杂度

- 时间复杂度：$O(n^2)$
- 空间复杂度：$O(n)$

### 代码

```java
class Solution {
    // DP + 空间压缩
    public int minInsertions(String s) {
        char[] str = s.toCharArray();
        int n = str.length;
        int[] dp = new int[n];
        for (int i = n - 2, prev; i >= 0; i--) {
            prev = dp[i + 1];
            dp[i + 1] = str[i] == str[i + 1] ? 0 : 1;
            for (int j = i + 2, temp; j < n; j++) {
                temp = dp[j];
                if (str[i] == str[j]) {
                    dp[j] = prev;
                } else {
                    dp[j] = 1 + Math.min(dp[j - 1], dp[j]);
                }
                prev = temp;
            }
        }
        return dp[n - 1];
    }
    // DP
    public int minInsertions1(String s) {
        char[] str = s.toCharArray();
        int n = str.length;
        int[][] dp = new int[n][n];
        for (int i = 0; i < n - 1; i++) {
            dp[i][i + 1] = str[i] == str[i + 1] ? 0 : 1;
        }
        for (int i = n - 3; i >= 0; i--) {
            for (int j = i + 2; j < n; j++) {
                if (str[i] == str[j]) {
                    dp[i][j] = dp[i + 1][j - 1];
                } else {
                    dp[i][j] = 1 + Math.min(dp[i][j - 1], dp[i + 1][j]);
                }
            }
        }
        return dp[0][n - 1];
    } 
    // 暴力递归
    public int process(char[] str, int left, int right) {
        if (left == right) {
            return 0;
        }
        if (left + 1 == right) {
            return str[left] == str[right] ? 0 : 1;
        }
        int res;
        if (str[left] == str[right]) {
            res = process(str, left + 1, right - 1);
        } else {
            res = 1 + Math.min(process(str, left, right - 1), process(str, left + 1, right));
        }
        return res;
    }
}
```