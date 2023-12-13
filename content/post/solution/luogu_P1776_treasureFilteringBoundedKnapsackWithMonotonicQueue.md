---
title: "洛谷 P1776 宝物筛选（单调队列优化）"
date: 2023-12-13T17:09:06+08:00
draft: false
math: true

tags: ["洛谷", "DP", "背包问题", "多重背包", "单调队列"]
categories: ""
description: "根据物品重量的余数，将最大重量进行分组，使用单调队列，分别对组内的 dp 结果求区间的最大值，这样就可以快速地获取最大重量时的最优结果，求出最优解。"
---

> [洛谷 P1776 宝物筛选](https://www.luogu.com.cn/problem/P1776)
>
> [算法讲解075【必备】背包dp-多重背包、混合背包](https://www.bilibili.com/video/BV1Nz4y1c71M/)

### 思路

在无优化版本的基础上进行优化，根据物品重量的余数，将最大重量进行分组，使用单调队列，分别对组内的 dp 结果求区间的最大值，这样就可以快速地获取最大重量时的最优结果，求出最优解。

### 求解过程

**根据余数分组**

首先在无优化版本中，`dp[i][j]` 的 `j` 是线性变化的，单位为 1。而物品的重量为 $w_i$，求上一个装载 `k - 1` 个物品的最大价值时，依赖的是 `dp[i - 1][j - w[i]] + v[i]`。如果依次求余数为 `[0 -> w[i] - 1]` 的组，它不会缺少依赖，时间复杂度也跟之前一样。

这样，我们可以维护一个大小为 $cnt_i \times w_i$ 的窗口，表示当前可以选择的物品重量范围，在这个窗口内，选择任意个物品的结果都会进入单调队列，并在队列头部存储窗口内的最大值，这样，我们就可以直接选择最优解，时间复杂度也不高。

**DP 过程**

base case dp[0][0] = 0。遍历每一种物品，在循环中依次以 `0, 1, 2, ..., wi - 1` 为开头，跨度为 `w[i]` 地进行遍历。在每一轮遍历中，使用一个单调栈记录下区间最大值：

1. 将当前 j 位置的结果进队，保持队列呈严格递增序。
2. 判断左边界 `j - (cnt[i] + 1) * w[i]` 是否为最大值的索引，如果是，队列头部右移。因为区间大小是 `cnt[i] * w[i]`，+ 1 是在这一轮循环中滑窗之外的，需要去掉。
3. 现在单调队列的窗口范围是正确的，求出 dp[i][j] 的最大值（取队头）。

**空间压缩**

分析依赖关系，当前 [i,j] 位置的 dp 结果依赖上一行 `<= j` 的位置，所以在使用一行时，需要从后向前遍历。同时，因为使用了单调队列，它维持的是在 i - 1 行时，窗口内的最大值，所以在开始遍历之前，`[j - cnt[i] * w[i], j]` 范围内的单调队列。

### 复杂度

- 时间复杂度：$n \times m$
- 空间复杂度：$m$

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
    public static int[] deque = new int[MAXW];
    public static int n, w, h, t;

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
            out.println(compute3());
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
    // 单调队列优化
    public static int compute2() {
        int[][] dp = new int[n + 1][w + 1];
        for (int i = 1; i <= n; i++) {
            // 使用余数作为遍历开始
            for (int spare = 0; spare <= Math.min(w, weight[i] - 1); spare++) {
                h = t = 0;
                for (int j = spare; j <= w; j += weight[i]) {
                    while (h < t && dp[i - 1][deque[t - 1]] + diffValue(j - deque[t - 1], i) <= dp[i - 1][j]) {
                        t--;
                    }
                    deque[t++] = j;
                    if (deque[h] == j - (cnt[i] + 1) * weight[i]) {
                        h++;
                    }
                    dp[i][j] = dp[i - 1][deque[h]] + diffValue(j - deque[h], i);
                }
            }
        }
        return dp[n][w];
    }
    // 例如前后两个结果，相差 diff 重量，求他们差的 value 是多少
    public static int diffValue(int diff, int i) {
        return diff / weight[i] * value[i];
    }
    // 单调队列优化 + 空间优化
    public static int compute3() {
        Arrays.fill(dp, 1, w + 1, 0);
        for (int i = 1; i <= n; i++) {
            // 使用余数作为遍历开始
            for (int spare = 0; spare <= Math.min(w, weight[i] - 1); spare++) {
                h = t = 0;
                // 先把单调队列初始化一下
                for (int j = w - spare, k = 0; j >= 0 && k <= cnt[i]; j -= weight[i], k++) {
                    while (h < t && dp[j] + diffValue(deque[t - 1] - j, i) >= dp[deque[t - 1]]) {
                        t--;
                    }
                    deque[t++] = j;
                }
                for (int j = w - spare; j >= 0; j -= weight[i]) {
                    dp[j] = dp[deque[h]] + diffValue(j - deque[h], i);
                    if (deque[h] == j) {
                        h++;
                    }
                    int enter = j - (cnt[i] + 1) * weight[i];
                    if (enter >= 0) {
                        while (h < t &&  dp[enter] + diffValue(deque[t - 1] - enter, i) >= dp[deque[t - 1]]) {
                            t--;
                        }
                    }
                    deque[t++] = enter;
                }
            }
        }
        return dp[w];
    }
}
```