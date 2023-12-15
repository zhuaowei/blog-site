---
title: "洛谷 P3205 [HNOI2010] 合唱队"
date: 2023-12-15T22:00:55+08:00
draft: false
math: true

tags: ["洛谷", "DP", "区间DP"]
categories: ""
description: "按照入队的逆序进行想，入队的时候肯定是在左右两边。如果左侧是最后入队的，则剩下的区间中也有两种入队情况，判断左侧入队是否满足这两种条件，若满足则方案数增加。"
---

> [洛谷 P3205 [HNOI2010] 合唱队](https://www.luogu.com.cn/problem/P3205)
>
> [算法讲解077【必备】区间dp-下](https://www.bilibili.com/video/BV1du4y1L7gy/)

### 思路

按照入队的逆序进行想，则子问题应该是：在区间 `[left, right]` 中，如果左侧是最后入队的，则在剩下的区间 `left + 1, right]` 中，又有两种情况。如果 $nums_{left + 1}$ 最后入队，则 $nums_{left}$ 应该大于它;如果 $nums_{right}$ 最后入队，则 $nums_{left}$ 应该大于它，则 `[left, right]` 区间中左侧入队的方案数为符合条件的可能性之和。

同理，右侧作为最后入队的时候，也分为两种情况。

### 求解过程

求解子问题：在区间 `[i, j]` 中，求出组成目标队列顺序的方案数。

因为同一个区间分为左右两侧分别是最后入队的情况，所以再使用一个纬度记录是否是左侧最后入队。在本文代码中，0 表示是左侧最后入队，1 表示右侧最后入队。

**Base Case**

1. `i == j`：只有一个人，作为最初始的情况，本来就在在队伍中，返回 0。
2. `i + 1 == j`：根据大小判断每种入队的方案数。如果 $height_i < height_j$，i 和 j 最后入队的方案之后一种。如果 $height_i > height_j$，无论谁先入队都无法满足要求，都是 0 。

**递归过程**

1. $height_i$ 最后入队，讨论在区间 `[i + 1, j]`的入队情况：

    a. $height_{i + 1}$ 最后入队，且 $height_i < height_{i + 1}$，满足入队顺序，方案数加上 `[i + 1, j]` 区间 $height_{i + 1}$ 后入队的方案数；否则不加。

    b. $height_{j}$ 最后入队，且 $height_i < height_{j}$，满足入队顺序，方案数加上 `[i + 1, j]` 区间 $height_{j}$ 后入队的方案数；否则不加。
2. 同理，讨论 $height_j$ 最后入队，讨论在区间 `[i, j - 1]`的入队情况。

### 复杂度

- 时间复杂度：$O(n^2)$
- 空间复杂度：$O(n)$

### 代码

```java
import java.io.*;

public class Main {
    public static int MOD = 19650827;
    public static int MAXN = 1001;
    public static int[] height = new int[MAXN];
    public static int[][] dp = new int[MAXN][2];
    public static int n;

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int) in.nval;
            for (int i = 1; i <= n; i++) {
                in.nextToken();
                height[i] = (int) in.nval;
            }
            if (n == 1) {
                out.println(1);
            } else {
                out.println(compute2());
            }
        }
        out.flush();
        out.close();
        br.close();
    }

    public static int compute1() {
        int[][][] dp = new int[n + 1][n + 1][2];
        for (int i = 1; i < n; i++) {
            if (height[i] < height[i + 1]) {
                dp[i][i + 1][0] = 1;
                dp[i][i + 1][1] = 1;
            }
        }
        for (int i = n - 2; i >= 1; i--) {
            for (int j = i + 2; j <= n; j++) {
                // 如果 i 是最后放进去的
                if (height[i] < height[i + 1]) {
                    dp[i][j][0] = (dp[i][j][0] + dp[i + 1][j][0]) % MOD;
                }
                if (height[i] < height[j]) {
                    dp[i][j][0] = (dp[i][j][0] + dp[i + 1][j][1]) % MOD;
                }
                if (height[j] > height[i]) {
                    dp[i][j][1] = (dp[i][j][1] + dp[i][j - 1][0]) % MOD;
                }
                if (height[j] > height[j - 1]) {
                    dp[i][j][1] = (dp[i][j][1] + dp[i][j - 1][1]) % MOD;
                }
            }
        }
        return (dp[1][n][0] + dp[1][n][1]) % MOD;
    }

    public static int compute2() {
        if (height[n - 1] < height[n]) {
            dp[n][0] = 1;
            dp[n][1] = 1;
        } else {
            dp[n][0] = 0;
            dp[n][1] = 0;
        }
        for (int i = n - 2; i >= 1; i--) {
            if (height[i] < height[i + 1]) {
                dp[i + 1][0] = 1;
                dp[i + 1][1] = 1;
            } else {
                dp[i + 1][0] = 0;
                dp[i + 1][1] = 0;
            }
            for (int j = i + 2, a, b; j <= n; j++) {
                a = 0;
                b = 0;
                // 如果 i 是最后放进去的
                if (height[i] < height[i + 1]) {
                    a = (a + dp[j][0]) % MOD;
                }
                if (height[i] < height[j]) {
                    a = (a + dp[j][1]) % MOD;
                }
                if (height[j] > height[i]) {
                    b = (b + dp[j - 1][0]) % MOD;
                }
                if (height[j] > height[j - 1]) {
                    b = (b + dp[j - 1][1]) % MOD;
                }
                dp[j][0] = a;
                dp[j][1] = b;
            }
        }
        return (dp[n][0] + dp[n][1]) % MOD;
    }
}
```