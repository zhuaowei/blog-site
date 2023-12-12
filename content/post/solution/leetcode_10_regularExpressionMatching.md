---
title: "LeetCode 10. 正则表达式匹配"
date: 2023-12-12T15:34:17+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "背包问题", "完全背包"]
categories: ""
description: "先写出暴力递归的版本，然后写出记忆化搜索版本和 DP 版本。因为出现 * 的时候，可以选择匹配 0 个或者多个字符，所以是一个完全背包问题。"
---

> [LeetCode 10. 正则表达式匹配](https://leetcode.cn/problems/regular-expression-matching/)
>
> [算法讲解074【必备】背包dp-分组背包、完全背包](https://www.bilibili.com/video/BV1UM411f7YL/)

### 思路

使用暴力递归，然后根据暴力递归写出记忆化搜索版本或者直接到 DP 版本。暴力递归中，当 s 串来到 i 位置，p 串来到 j 位置，判断 i,j 之后的字符串能够匹配。

### 求解过程

**暴力递归的base case：**
1. `i` 到达了 `s` 的末尾：如果 `j` 也到达了 `p` 的末尾，匹配成功；如果 j 没有到达末尾，判断 `p` 串剩余的能否匹配空串，即剩余部分应为类似 `"a*.*"` 这种串。
2. `i` 没有到达结尾，但是 `j` 到达了末尾，肯定无法匹配。无法用一个空的模板匹配非空串。

**递归过程**

1. 如果 `j + 1` 不越界且为 `*`：
   
   a. 让 `[j, j + 1]` 匹配空串，继续匹配。

   b. 让 `[j, j + 1]` 匹配一个字符，然后继续匹配。

2. 如果 `j + 1` 越界或者 `j + 1` 不为 `*`，必须匹配单个字符：

    a. 匹配成功（`s[i] == p[j] || p[j] == '.'`）： 继续匹配。

    b. 匹配失败：返回 `false`。

写出递归过程后，根据递归的 base case 和 递归过程，写出记忆化搜索版本和 DP 版本。

### 复杂度

- 时间复杂度：$O(n \times m)$
- 空间复杂度：$O(n \times m)$

### 代码

```java
class Solution {

    public boolean isMatch(String s, String p) {
        char[] str = s.toCharArray();
        char[] pat = p.toCharArray();
        int n = str.length, m = pat.length;
        boolean[][] dp = new boolean[n + 1][m + 1];
        dp[n][m] = true;
        for (int j = m - 1; j >= 0; j--) {
            dp[n][j] = j + 1 < m && pat[j + 1] == '*' && dp[n][j + 2];
        }
        for (int i = n - 1; i >= 0; i--) {
            // dp[i][m] = false;
            for (int j = m - 1; j >= 0; j--) {
                if (j + 1 < m && pat[j + 1] == '*') {
                    // 把模板消掉，看看能不能匹配
                    dp[i][j] = dp[i][j + 2] || ((str[i] == pat[j] || pat[j] == '.') && dp[i + 1][j]);
                } else {
                    dp[i][j] = ((str[i] == pat[j] || pat[j] == '.') && dp[i + 1][j + 1]);
                }
            }
        }
        return dp[0][0];
    }
    // 暴力递归，也能过
    public boolean process(char[] s, char[] p, int i, int j) {
        if (i == s.length) {
            // i 到头了 ，j 也到头了
            if (j == p.length) {
                return true;
            } else {
                // j 没到头，但是末尾可以消除，消除后能到头，也返回 true，否则返回 false
                return j + 1 < p.length && p[j + 1] == '*' ? process(s, p, i, j + 2) : false;
            }
        }
        if (j == p.length) {
            // i 没到头，j 到头了，返回 false
            return false;
        }
        boolean res = false;
        // 剩下就是都没到头的情况
        if (j + 1 < p.length && p[j + 1] == '*') {
            // 把模板消掉，看看能不能匹配
            res = res || process(s, p, i, j + 2);
            if (s[i] == p[j] || p[j] == '.') {
                // 可以匹配，继续匹配
                res = res || process(s, p, i + 1, j);
            }
        } else {
            // 只匹配一个字符
            if (s[i] == p[j] || p[j] == '.') {
                res = res || process(s, p, i + 1, j + 1);
            } else {
                res = res || false;
            }
        }
        return res;
    }
}
```