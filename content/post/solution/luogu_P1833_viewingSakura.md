---
title: "洛谷 P1833 赏樱花"
date: 2023-12-13T17:15:13+08:00
draft: false
math: true

tags: ["洛谷", "DP", "背包问题", "混合背包"]
categories: ""
description: "这道题是一个混合背包题目，物品可以选  0 - k 个，也可以选无限多个。通过转化，将完全背包转化为多重背包，再通过二进制分组，将多重背包转化为 01 背包。"
---

> [洛谷 P1833 赏樱花](https://www.luogu.com.cn/problem/P1833)
>
> [算法讲解075【必备】背包dp-多重背包、混合背包](https://www.bilibili.com/video/BV1Nz4y1c71M/)

### 思路

通过转化，将完全背包转化为多重背包，再通过二进制分组，将多重背包转化为 01 背包。如果物品数量有限，通过二进制分组转化为 01 背包；如果物品数量无限，根据最大花费和物品价格计算出可以选择的最大数量，转化为多重背包，再转化为 01 背包。

### 求解过程

> 多重背包二进制分组的原理：[洛谷 P1776 宝物筛选（二进制分组优化）](/article/洛谷-p1776-宝物筛选二进制分组优化/)

读取时，根据物品的数量进行二进制分组。如果物品数量为 0，设置为物品的最大数量进行二进制分组；如果是其他，直接进行二进制分组。

分完组后，使用 01 背包的方式求解。

### 复杂度

- 时间复杂度：$O(\sum_{i = 1}^{N}{(1 + log_2P_i)} \times T)$

N 为樱花种类数量，P 为观赏次数，T 为剩余时间。

- 空间复杂度：$O(T)$

### 代码

```java
import java.io.*;
import java.util.Arrays;

public class Main {
    public static int MAXN = 100001;
    public static int MAXT = 1001;
    public static int[] time = new int[MAXN];
    public static int[] value = new int[MAXN];
    public static int[] dp = new int[MAXT];
    public static int n, t, m;
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            int startHour, startMinute, endHour, endMinute;
            startHour = (int)in.nval; in.nextToken();
            in.nextToken();
            startMinute = (int)in.nval; in.nextToken();
            endHour = (int)in.nval; in.nextToken();
            in.nextToken();
            endMinute = (int)in.nval; in.nextToken();
            // 计算剩余时间
            t = (endMinute + 60 - startMinute) + (endHour - startHour - 1) * 60;
            n = (int)in.nval;
            m = 0;
            for (int i = 1, cost, val, cnt; i <= n; i++) {
                in.nextToken(); cost = (int)in.nval;
                in.nextToken(); val = (int)in.nval;
                in.nextToken(); cnt = (int)in.nval;
                if (cnt == 0) {
                    cnt = MAXT;
                }
                for (int r = 1; r <= cnt; r <<= 1) {
                    time[++m] = r * cost;
                    value[m] = r * val;
                    cnt -= r;
                }
                if (cnt > 0) {
                    time[++m] = cnt * cost;
                    value[m] = cnt * val;
                }
            }
            out.println(compute());
        }
        out.flush();
        out.close();
        br.close();
    }

    public static int compute() {
        Arrays.fill(dp, 1, t + 1, 0);
        for (int i = 1; i <= m; i++) {
            for (int j = t; j >= time[i]; j--) {
                dp[j] = Math.max(dp[j], dp[j - time[i]] + value[i]);
            }
        }
        return dp[t];
    }
}
```