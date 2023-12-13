---
title: "洛谷 P1776 宝物筛选（无枚举优化）"
date: 2023-12-13T17:08:06+08:00
draft: false
math: true

tags: ["洛谷", "DP", "背包问题", "多重背包"]
categories: ""
description: "多重背包相较于完全背包和 01 背包，它的物品数量是有限的，它的每个物品可以选 0 ... cnt 个。所以可以在 01 背包的基础上再套一层循环，来限制选择的个数。"
---

> [洛谷 P1776 宝物筛选](https://www.luogu.com.cn/problem/P1776)
>
> [算法讲解075【必备】背包dp-多重背包、混合背包](https://www.bilibili.com/video/BV1Nz4y1c71M/)

### 思路

首先说一下，这个不优化的版本是无法通过全部测试用例的。

多重背包相较于完全背包和 01 背包，它的物品数量是有限的，它的每个物品可以选 0 ... cnt 个。所以可以在 01 背包的基础上再套一层循环，来限制选择的个数。

### 求解过程

**base case**

前 0 个物品选择价值为小于等于 j 的最小花费是 0。

**DP 过程**

1. 选 0 个：`dp[i - 1][j]`
2. 选 1 个：`dp[i - 1][j - cost[i]] + value[i]`
3. 选 k 个：`dp[i - 1][j - k * cost[i]] + k * value[i]`

上述选 k 个的前提是当前的钱数能够买 k 个。

### 复杂度

- 时间复杂度：$O(n \times m \times \sum_{k = 1}^{cnt}count_i)$

n 为物品种类数量，m 为最大花费，$\sum_{k = 1}^{cnt}count_i)$ 为所有物品的总数量。这个时间复杂度很大，不进行优化是无法通过测试全部用例的。

- 空间复杂度：$O(m)$

### 代码

```java
import java.io.*;
import java.util.Arrays;

public class Main {
    public static int MAXN = 101;
    public static int MAXW = 40001;
    public static int[] weight = new int[MAXN];
    public static int[] value = new int[MAXN];
    public static int[] cnt = new int[MAXN];
    public static int[] dp = new int[MAXW];
    public static int n, w;

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int) in.nval;
            in.nextToken();
            w = (int) in.nval;
            for (int i = 1; i <= n; i++) {
                in.nextToken();
                value[i] = (int) in.nval;
                in.nextToken();
                weight[i] = (int) in.nval;
                in.nextToken();
                cnt[i] = (int) in.nval;
            }
            out.println(compute2());
        }
        out.flush();
        out.close();
        br.close();
    }

    // 严格位置依赖的动态规划
    public static int compute1() {
        int[][] dp = new int[n + 1][w + 1];
        for (int i = 1; i <= n; i++) {
            for (int j = 0; j <= w; j++) {
                dp[i][j] = dp[i - 1][j];
                for (int k = 1; k <= cnt[i] && k * weight[i] <= j; k++) {
                    dp[i][j] = Math.max(dp[i][j], dp[i - 1][j - k * weight[i]] + k * value[i]);
                }
            }
        }
        return dp[n][w];
    }
    // 时间复杂度太高
    public static int compute1() {
        Arrays.fill(dp, 0, w + 1, 0);
        for (int i = 1; i <= n; i++) {
            // dp[i][j] = dp[i - 1][j]
            for (int j = w; j >= cost[i]; j--) {
                for (int k = 1; k <= cnt[i] && k * cost[i] <= j; k++) {
                    dp[j] = Math.max(dp[j], dp[j - k * cost[i]] + k * value[i]);
                }
            }
        }
        return dp[w];
    }
}

```