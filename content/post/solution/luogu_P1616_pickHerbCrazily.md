---
title: "洛谷 P1616 疯狂的采药"
date: 2023-12-11T17:32:30+08:00
draft: false
math: true

tags: ["洛谷", "DP", "背包问题", "完全背包"]
categories: ""
description: "完全背包的模板题。因为每一种物品都可以无线选，所以在选择前 i 件物品时，可以在选择了 i 的基础上再次选择第 i 件物品。"
---

> [洛谷 P1616 疯狂的采药](https://www.luogu.com.cn/problem/P1616)
> 
> B站左程云：[算法讲解074【必备】背包dp-分组背包、完全背包](https://www.bilibili.com/video/BV1UM411f7YL/)

### 思路

完全背包和 01 背包的区别：01 背包每个物品只能选一次，选或不选；完全背包每个物品可以无限选，选择 n 件（n = 0, 1, 2, 3...）。

因为每一种物品都可以无限选，所以在来到第 i 件物品时：

1. 如果一件都不选：$dp[i][j] = dp[i - 1][j]$

2. 如果选，在之前已经选了 n 件的基础上（n = 0, 1, 2, 3...），再次选择一件：$dp[i][j] = dp[i][j - cost_i] + value_i$。

在上述各种可能里选择最大的结果。

### 求解过程

根据上述过程，对每一件物品计算选择 n 件的结果，取最大值。

**空间压缩**

这题数据量很大，只能进行空间压缩才能过，否则会 TLE。

如果只用一行进行 DP，分析一下 $dp[i][j]$ 的依赖：

1. 如果不选，它依赖上一行的 $dp[j]$，因为只有一行，不用改。
2. 如果选择，它依赖本行的 $dp[j - cost_i]$，所以在求 $dp[j]$ 时，应该先计算好本行索引比自己小的位置，所以**从左往右遍历**。(01 背包是从右往左)

### 复杂度

- 时间复杂度：$O(n \times m)$
- 空间复杂度：$O(m)$

### 代码

```java
import java.io.*;
import java.util.Arrays;

public class Main {
    public static int MAXT = (int)1e7 + 1;
    public static int MAXM = (int)1e4 + 1;
    public static int[] cost = new int[MAXM];
    public static int[] value = new int[MAXM];
    public static long[] dp = new long[MAXT];
    public static int t, m;
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            t = (int)in.nval; in.nextToken();
            m = (int)in.nval;
            for (int i = 1; i <= m; i++) {
                in.nextToken(); cost[i] = (int)in.nval;
                in.nextToken(); value[i] = (int)in.nval;
            }
            out.println(compute2());
        }
        out.flush();
        out.close();
        br.close();
    }
    // 未压缩空间
    public static long compute1() {
        long[][] dp = new long[MAXM][MAXT];
        for (int i = 1; i <= m; i++) {
            for (int j = 0; j <= t; j++) {
                dp[i][j] = dp[i - 1][j];
                if (j >= cost[i]) {
                    dp[i][j] = Math.max(dp[i][j], dp[i][j - cost[i]] + value[i]);
                }
            }
        }
        return dp[m][t];
    }
    // 空间压缩
    public static long compute2() {
        Arrays.fill(dp, 1, t + 1, 0);
        for (int i = 1; i <= m; i++) {
            for (int j = cost[i]; j <= t; j++) {
                dp[j] = Math.max(dp[j], dp[j - cost[i]] + value[i]);
            }
        }
        return dp[t];
    }
}
```