---
title: "LeetCode 44. 通配符匹配"
date: 2023-12-12T16:38:58+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "背包问题", "完全背包"]
categories: ""
description: "字符串匹配的相似题目，比字符串匹配稍微简单一些。同样还是先写出暴力递归版本，再改成记忆化搜索版本和 DP 版本。本文给出了暴力递归版本和最终的空间压缩版本。"
---

> [LeetCode 44. 通配符匹配](https://leetcode.cn/problems/wildcard-matching/)
>
> [算法讲解074【必备】背包dp-分组背包、完全背包](https://www.bilibili.com/video/BV1UM411f7YL/)

### 思路

首先先写出暴力递归的版本，在 s 串来到 i，p 串来到 j 时，判断 i,j 之后的字符串能否匹配。写出递归调用的 base case 和 递归调用过程。

### 求解过程

**base case**

1. 当 `i` 来到末尾时：

    a. `j` 也来到末尾：匹配成功，返回 `true`。

    b. `j` 没有来到末尾：查看 `j` 及之后的字符是否全是 `*`，如果全是，返回 `true`，否则返回 `false`。

2. 当 `i` 没有到末尾，`j` 来到了末尾时：肯定无法匹配，返回 `false`。

**递归过程**

1. 如果 `p[j] == '*'`: 选择匹配 0 个或者 1 个字符，继续匹配。
2. 如果 `p[j] != '*'`: 必须匹配一个字符。如果 `str[i] == pat[j] || pat[j] == '?'`，继续匹配，否则返回 `false`。

写出来暴力递归后，是无法通过的，需要修改为记忆化搜索或者 DP 版本。根据 base case 和递归调用过程修改为 DP 版本，下面是最终的空间压缩版本。

### 复杂度

- 时间复杂度：$O(n \times m)$
- 空间复杂度：$O(m)$

### 代码

```java
class Solution {
    public boolean isMatch(String s, String p) {
        char[] str = s.toCharArray();
        char[] pat = p.toCharArray();
        int n = str.length, m = pat.length;
        boolean[] dp = new boolean[m + 1];
        dp[m] = true;
        for (int j = m - 1; j >= 0; j--) {
            if (pat[j] != '*') {
                break;
            }
            dp[j] = true;
        }
        for (int i = n - 1; i >= 0; i--) {
            // dp[i][m] = false;
            boolean prev = dp[m], temp;
            dp[m] = false;
            for (int j = m - 1; j >= 0; j--) {
                temp = dp[j];
                if (pat[j] == '*') {
                    dp[j] =  dp[j] || dp[j + 1];
                } else {
                    dp[j] = (str[i] == pat[j] || pat[j] == '?') && prev;
                }
                prev = temp;
            }
        }

        return dp[0];
    }
    // 暴力递归
    public boolean process(char[] str, char[] pat, int i, int j) {
        // i 到头了
        if (i == str.length) {
            if (j == pat.length) {
                // j 也到头了，返回 true
                return true;
            } else {
                // 如果后面全是 * 返回true，否则返回 false
                while (j < pat.length) {
                    if (pat[j] != '*') {
                        return false;
                    }
                    j++;
                }
                return true;
            }
        }
        // i 没到头，j 到头了，返回 false
        if (j == pat.length) {
            return false;
        }
        // i j 都没到头
        if (pat[j] == '*') {
            return process(str, pat, i, j + 1) || process(str, pat, i + 1, j);
        } else {
            return (str[i] == pat[j] || pat[j] == '?') && process(str, pat, i + 1, j + 1);
        }
    }
}
```