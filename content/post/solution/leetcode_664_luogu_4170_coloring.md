---
title: "LeetCode 664. 奇怪的打印机 | 洛谷 P4170 [CQOI2007] 涂色"
date: 2023-12-15T17:13:36+08:00
draft: false
math: true

tags: ["LeetCode", "洛谷", "DP", "区间DP"]
categories: ""
description: "定义子问题：在区间 [left, right] 涂色的最小次数。因为后涂的颜色可以覆盖前一种颜色，如果我们要让涂色次数最少，可以使用贪心，让左侧的颜色尽量涂多一些。"
---

> [LeetCode 664. 奇怪的打印机](https://leetcode.cn/problems/strange-printer/)
>
> [洛谷 P4170 [CQOI2007] 涂色](https://www.luogu.com.cn/problem/P4170)
>
> [算法讲解077【必备】区间dp-下](https://www.bilibili.com/video/BV1du4y1L7gy/)

### 思路

定义子问题：在区间 `[left, right]` 涂色的最小次数。因为后涂的颜色可以覆盖前一种颜色，如果我们要让涂色次数最少，可以使用贪心，让左侧的颜色尽量涂多一些。

### 求解过程

讨论在子问题在区间 `[left, right]` 涂色的最小次数。

**Base Case**

1. 如果区间值剩下一个位置，可以涂 1 次。
2. 如果区间剩下两个位置，如果颜色一样，涂 1次，否则涂 2 次。

**递归过程**

1. 如果左右两端颜色一样，我们可以认为这两种是一次性涂好的，然后继续判断在 `[left, right - 1]` 范围是否需要再涂。
2. 如果左右两端颜色不一样，说明左侧的颜色涂到的位置未知，右侧颜色开始的位置也未知，但是他们应该是相邻的。分别以区间中的每一个点作为分割点，计算出左右区间的最小涂色次数之和。在所有可能性中取最小值。

**状态转移方程**

$$
\begin{equation}
\begin{aligned}
f(i, j) = \begin{cases}
    1 && i == j\\\\
    \begin{cases}
       1 && 两端颜色一致\\\\
       2 && 两端颜色不一致
    \end{cases} && i + 1 == j\\\\
    \begin{cases}
        f(i, j - 1) && 两端颜色一致\\\\
        \min(f(i, k) + f(k + 1, j)) && k = i, i + 1, ..., j - 1
    \end{cases} && i + 1 < j
\end{cases}
\end{aligned}
\end{equation}
$$

### 复杂度

### 代码

```java
import java.io.*;

public class Main {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        String s = br.readLine();
        out.println(strangePrinter(s));
        out.flush();
        out.close();
        br.close();
    }

    public static int strangePrinter(String s) {
        char[] str = s.toCharArray();
        int n = str.length;
        int[][] dp = new int[n][n];
        for (int i = 0; i < n; i++) {
            dp[i][i] = 1;
        }
        for (int i = n - 2; i >= 0; i--) {
            for (int j = i + 1; j < n; j++) {
                if (str[i] == str[j]) {
                    dp[i][j] = dp[i][j - 1];
                } else {
                    int ans = Integer.MAX_VALUE;
                    for (int k = i; k < j; k++) {
                        ans = Math.min(ans, dp[i][k] + dp[k + 1][j]);
                    }
                    dp[i][j] = ans;
                }
            }
        }
        return dp[0][n - 1];
    }
}
```