---
title: "ACWing 01背包问题"
date: 2023-12-10T11:59:20+08:00
draft: false
math: true

tags: ["ACWing", "DP", "背包问题", "01背包问题"]
categories: ""
description: "经典背包问题，考虑每个物品选与不选，在不超过背包容量的情况下获取的物品价值的最大值。"
---

> [ACWing 01背包问题](https://www.acwing.com/problem/content/2/)
>
> [算法讲解073【必备】背包dp-01背包、有依赖的背包](https://www.bilibili.com/list/)

### 思路

考虑每一件物品，当前来到了第 i 件物品，背包容量为 j。如果选了第 i 件物品，获得的价值是 $w_i$，如果要装下该物品，则背包需要预留给这件物品足够的空间，也就是背包在选择前 i - 1 件物品后，背包容量最大应该为 $j - v_i$。

如果不选，则不需要预留空间，背包的价值和选择前 i - 1 件物品时是一样的。

所以我们可以得到一个状态转移方程：

$$f(i,j) = max
\begin{cases}
f(i-1,j) & 不选\\\\
f(i-1,j-v_i) + w_i  & j-v_i \ge 0，选
\end{cases}
$$

### 求解过程

遍历每一个物品，计算背包容量从 0 到 v 的最大值，所以有两层循环。

**空间压缩**

当我们遍历到第 i 件物品时，根据转移方程，它只依赖前一个物品的`背包容量 <= j` 的结果。在二维表中的表现就是 dp[i][j] 只依赖第 `i - 1` 行小于等于 j 的值，如果使用一维表的话，只使用一行。如果不选，第 `i` 行的 `dp[j]`  就等于第 `i - 1` 行的 `dp[j]`，所以不用改；如果选的话，第 `i` 行的 `dp[j]` = 第 `i - 1` 行的 `dp[j - vi] + wi`，由于后面依赖前面，不能从左往右填，要从右往左填。`j` 左边的是第 `i - 1` 行的，右边是第 `i` 行的。

### 复杂度

- 时间复杂度：$O(N \times M)$

N  为物品数量，M 为背包容量。就是将 N * M 的表格填好，返回右下角的值。

- 空间复杂度：$O(M)$

压缩空间后的空间只需要一行即可，为背包容量大小。

### 代码

```java
import java.io.*;
import java.util.Arrays;

public class Main {
    public static int MAXN = 1005;
    public static int[] values = new int[MAXN];
    public static int[] volumes = new int[MAXN];
    public static int[] dp = new int[MAXN];
    public static int n, v;
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int)in.nval;
            in.nextToken();
            v = (int)in.nval;
            for (int i = 0; i < n; i++) {
                in.nextToken(); volumes[i] = (int)in.nval;
                in.nextToken(); values[i] = (int)in.nval;
            }
            System.out.println(compute());
        }
        out.flush();
        out.close();
        br.close();
    }

    public static int compute() {
        Arrays.fill(dp, 0, v + 1, 0);
        // 01 背包模板
        for (int i = 0; i < n; i++) {
            for (int j = v; j >= volumes[i]; j--) {
                dp[j] = Math.max(dp[j], dp[j - volumes[i]] + values[i]);
            }
        }
        return dp[v];
    }
}

```