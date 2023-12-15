---
title: "牛客网 括号区间匹配"
date: 2023-12-15T16:43:24+08:00
draft: false
math: true

tags: ["牛客网", "DP", "区间DP"]
categories: ""
description: "定义子问题：在区间 [left, right] 之间能够将括号变成有效的最小插入次数。"
---

> [牛客网 括号区间匹配](https://www.nowcoder.com/practice/e391767d80d942d29e6095a935a5b96b)
>
> [算法讲解077【必备】区间dp-下](https://www.bilibili.com/video/BV1du4y1L7gy/)

### 思路

定义子问题：在区间 `[left, right]` 之间能够将括号变成有效的最小插入次数。

### 求解过程

括号的组合有两种，一是嵌套 `[()]`，二是并列 `[]()`。在子问题中，讨论区间 `[left, right]` 两端的括号形成可能性。

1. 如果两侧端点可以匹配上，那么说明两端的括号可能属于同一组，讨论剩余的插入次数；也可能不是同一组，按下面一种方式讨论。
2. 如果两侧端点无法匹配，那么肯定不是一组，只讨论不是一组的情况。分别以区间内的每一个字符作为分割点，讨论左右两侧的最小插入数。

以上所有情况中取最小值，作为 `[left, right]` 区间的字符串的最小插入数。

**Base Case**

1. 当区间只剩一个字符时，肯定无法匹配，插入一个字符使其匹配。
2. 如果区间只剩两个字符，判断是否匹配，若匹配，返回 0，不匹配，返回 2。

**状态转移方程**

$$
\begin{equation}
\begin{aligned}
f(i,j) = \begin{cases}
    1 && i == j, \\\\
    0 && i + 1 == j 且 str_i 与 str_j 匹配\\\\
    2 && i + 1 == j 且 str_i 与 str_j 不匹配\\\\
    \min\begin{cases}
        f(i + 1, j - 1) && str_{i + 1} 与 str_j 匹配\\\\
        f(i, k) + f(k, j) && k = i, i + 1, i + 2, ..., j - 1
    \end{cases} && i + 1 < j
\end{cases}
\end{aligned} 
\end{equation}
$$

根据状态转移方程和上面的分析，很容易写出记忆化搜索版本和严格位置依赖的版本。

### 复杂度

- 时间复杂度：$O(n^3)$
- 空间复杂度：$O(n^2)$

### 代码

```java
import java.io.*;

public class Main {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        String s = br.readLine();
        out.println(compute(s));
        out.flush();
        out.close();
        br.close();
    }

    public static int compute(String s) {
        char[] str = s.toCharArray();
        int n = str.length;
        int[][] dp = new int[n][n];
        for (int i = 0; i < n; i++) {
            dp[i][i] = 1;
        }
        for (int i = 0; i < n - 1; i++) {
            // 不匹配的情况，剩下的两个字符不匹配的情况
            if (!((str[i] == '(' && str[i + 1] == ')') || (str[i] == '[' && str[i + 1] == ']'))) {
                dp[i][i + 1] = 2;
            }
        }
        for (int i = n - 3; i >= 0; i--) {
            for (int j = i + 2; j < n; j++) {
                int ans = Integer.MAX_VALUE;
                // 可以匹配
                if ((str[i] == '(' && str[j] == ')') || (str[i] == '[' && str[j] == ']')) {
                    ans = dp[i + 1][j - 1];
                }
                for (int k = i; k < j; k++) {
                    ans = Math.min(ans, dp[i][k] + dp[k + 1][j]);
                }
                dp[i][j] = ans;
            }
        }
        return dp[0][n - 1];
    }
}
```