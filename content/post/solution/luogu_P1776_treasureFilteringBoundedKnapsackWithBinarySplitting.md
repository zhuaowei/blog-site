---
title: "洛谷 P1776 宝物筛选（二进制分组优化）"
date: 2023-12-13T17:08:27+08:00
draft: false
math: true

tags: ["洛谷", "DP", "背包问题", "多重背包"]
categories: ""
description: "物品的数量可以分解为几个二进制数字的组合，通过对这几个二进制数的组合，可以表示在 `[0, cnt]` 之间选取任意个数量的物品。"
---

> [洛谷 P1776 宝物筛选](https://www.luogu.com.cn/problem/P1776)
>
> [算法讲解075【必备】背包dp-多重背包、混合背包](https://www.bilibili.com/video/BV1Nz4y1c71M/)

### 思路

物品的数量可以分解为几个二进制数字的组合，通过对这几个二进制数的组合，可以表示在 `[0, cnt]` 之间选取任意个数量的物品。

### 求解过程

**整数分解**

任何一个正整数都可以被分解成二进制的形式，例如 17 -> [1, 2, 4, 8, 2]。

具体的分解过程为：
1. 17 包含 1，分解为 1 + 16
2. 16 包含 2，分解为 2 + 14
3. 14 包含 4，分解为 4 + 10
4. 14 包含 8，分解为 8 + 2
5. 2 不包含 16，结束。最后分解为 2。

**组合** 

这些分解出来的数字可以组成任意 `[0, cnt]` 之间的数，也就是说它可以表示选取任意多个物品，但是它的时间复杂度仅为 $1 + \lfloor log_2target \rfloor$。

**数据处理**

在读取数据的时候，根据物品的数量分解，计算每个段的物品价值和花费，将每个分段的物品作为最终 DP 的物品列表进行计算，使用 01 背包的方式。

### 复杂度

- 时间复杂度：$O(\sum_{i = 1}^{n}{(1 + log_2cnt_i)} \times m)$
- 空间复杂度：$O(m)$

### 代码

```java
import java.io.*;
import java.util.Arrays;

public class Main {
    public static int MAXN = 1001;
    public static int MAXW = 40001;
    public static int[] cost = new int[MAXN];
    public static int[] value =  new int[MAXN];
    public static int[] cnt = new int[MAXN];
    public static int[] dp = new int[MAXW];
    public static int n, w, m;
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int)in.nval; in.nextToken();
            w = (int)in.nval;
            m = 0;
            for (int i = 1, v, weight, count, r; i <= n; i++) {
                in.nextToken(); v = (int)in.nval;
                in.nextToken(); weight = (int)in.nval;
                in.nextToken(); count = (int)in.nval;
                // 核心
                r = 1;
                while (r <= count) {
                    value[++m] = r * v;
                    cost[m] = r * weight;
                    count -= r;
                    r <<= 1;
                }
                if (count > 0) {
                    value[++m] = count * v;
                    cost[m] = count * weight;
                }
            }
            out.println(compute());
        }
        out.flush();
        out.close();
        br.close();
    }
    public static int compute()  {
        Arrays.fill(dp, 0, w + 1, 0);
        for (int i = 1; i <= m; i++) {
            for (int j = w; j >= cost[i]; j--) {
                dp[j] = Math.max(dp[j], dp[j - cost[i]] + value[i]);
            }
        }
        return dp[w];
    }
}
```