---
title: "洛谷 P1757 通天之分组背包"
date: 2023-12-11T17:27:37+08:00
draft: false
math: true

tags: ["洛谷", "DP", "背包问题", "分组背包"]
categories: ""
description: "在 01 背包的基础上添加分组信息，每一组的物品最多只能选一个，所以需要分组讨论，在花费不超过 j 的情况考虑组内每种物品的结果。"
---

> [洛谷 P1757 通天之分组背包](https://www.luogu.com.cn/problem/P1757)
> 
> B站左程云：[算法讲解074【必备】背包dp-分组背包、完全背包](https://www.bilibili.com/video/BV1UM411f7YL/)

### 思路

分组背包的模板题，先按照组号排序，对组内每一个物品求在花费不超过 j 时获得的最大价值。

### 求解过程

首先按照组号排序，确定组的左右边界，然后从左到右遍历每一组。在一组内：

1. 一个物品都不选：`dp[group][j] = dp[group - 1][j]`
2. 依次遍历组内每一个物品：`dp[group][j] = dp[group - 1][j - items[k][0]] + items[k][1]`

在上述结果中求最大值。

**空间压缩**

跟 01 背包类似，每一组只依赖前一组 `dp[group - 1][0 -> j]` 的值，所以从右往左遍历。

### 复杂度

- 时间复杂度：$O(n \times m)$

n 为物品数量，m 为最大花费。虽然 DP 是按照组来的，但是物品还是都只被遍历了一遍。

- 空间复杂度：$O(m)$

### 代码

```java
import java.io.*;
import java.util.Arrays;

public class Main {
    public static int MAXN = 1001;
    public static int MAXK = 101;
    public static int[][] items = new int[MAXN][3];
    public static int[] dp = new int[MAXN];
    public static int n, m;
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            m = (int)in.nval; in.nextToken();
            n = (int)in.nval;
            for (int i = 1; i <= n; i++) {
                in.nextToken(); items[i][0] = (int)in.nval;
                in.nextToken(); items[i][1] = (int)in.nval;
                in.nextToken(); items[i][2] = (int)in.nval;
            }
            out.println(compute1());
        }
        out.flush();
        out.close();
        br.close();
    }

    public static int compute1() {
        int[][] dp = new int[MAXK][m + 1];
        Arrays.sort(items, 1, n + 1, (item1, item2) -> item1[2] - item2[2]);
        int group = 0;
        for (int start = 1, end = 2; start <= n;) {
            while (end <= n && items[start][2] == items[end][2]) {
                end++;
            }
            group++;
            for (int j = 0; j <= m; j++) {
                dp[group][j] = dp[group - 1][j];
                for (int k = start; k < end; k++) {
                    if (j >= items[k][0]) {
                        dp[group][j] = Math.max(dp[group][j], dp[group - 1][j - items[k][0]] + items[k][1]);
                    }
                }
            }
            start = end++;
        }
        return dp[group][m];
    }
    public static int compute2() {
        Arrays.fill(dp, 1, m + 1, 0);
        Arrays.sort(items, 1, n + 1, (item1, item2) -> item1[2] - item2[2]);
        for (int start = 1, end = 2; start <= n; ) {
            while (end <= n && items[start][2] == items[end][2]) {
                end++;
            }
            for (int j = m; j >= 0; j--) {
                for (int k = start; k < end; k++) {
                    if (j >= items[k][0]) {
                        dp[j] = Math.max(dp[j], dp[j - items[k][0]] + items[k][1]);
                    }
                }
            }
            start = end++;
        }
        return dp[m];
    }
}
```