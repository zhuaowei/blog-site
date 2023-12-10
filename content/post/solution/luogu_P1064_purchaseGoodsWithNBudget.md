---
title: "洛谷 P1064 [NOIP2006 提高组] 金明的预算方案"
date: 2023-12-10T20:12:50+08:00
draft: false
math: true

tags: ["洛谷", "DP", "背包问题", "01背包问题"]
categories: ""
description: "物品分为两类，如果是主件，首先考虑只买主件，相当于01背包问题，然后再考虑主件下的附件；如果是附件，直接逃过。"
---

> [P1064 [NOIP2006 提高组] 金明的预算方案](https://www.luogu.com.cn/problem/P1064)
>
> [牛客网 购物单](https://www.nowcoder.com/practice/f9c6f980eeec43ef85be20755ddbeaf4)
>
> [算法讲解073【必备】背包dp-01背包、有依赖的背包](https://www.bilibili.com/list/)


### 思路

物品分为主件和附件，附件必须在购买主件后才能考虑购买。所以在求解过程中，判断是否是主件，如果是，就考虑选或者不选。然后考虑主件下的附件，因为附件最多只有 2 件，分为以下四种情况，不买附件，只买附件一，只买附件二，都买，

### 求解过程

使用数组分别记录下物品的价格、价值、是否是主件、附件的个数、有哪些附件。

然后开始01背包DP，当前来到 i 位置，上一个是主件的位置是 last。

1. 如果当前不是主件，直接跳过。
2. 如果是主件，预算不够买主件，不买的结果。预算够买主件，买的结果，求最大值。
   
   a. 不够买附件一，不买；够买附件一，买。求最大值。

   b. 不够买附件二，不买；够买附件二，买。求最大值。

   c. 不够买附件一和附件二，不买；够买附件一和附件二，买。求最大值。
3. 最后更新下上次遇到主件的下标。

下面的代码是空间压缩后的版本。

### 复杂度

- 时间复杂度：$O(N \times M)$

N 为物品的数量，M 为购物的预算。

- 空间复杂度：$O(M)$

### 代码

```java
import java.io.*;
import java.util.Arrays;

public class Main {
    public static int MAX_N = 32001;
    public static int MAX_M = 61;
    public static int[] prices = new int[MAX_M];    // 价格
    public static int[] values = new int[MAX_M];    // 重要度
    public static boolean[] kings = new boolean[MAX_M]; // 是否是主件
    public static int[] cnt = new int[MAX_M];       // 附件数量
    public static int[][] follows = new int[MAX_M][2];  // 附件
    public static int[] dp = new int[MAX_N];
    public static int n, m;

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int)in.nval; in.nextToken();
            m = (int)in.nval;
            Arrays.fill(cnt, 0, m + 1, 0);
            for (int i = 1, q; i <= m; i++) {
                in.nextToken(); prices[i] = (int)in.nval;
                in.nextToken(); values[i] = (int)in.nval * prices[i];
                in.nextToken(); q = (int)in.nval;
                kings[i] = q == 0;
                if (q != 0){
                    // 如果是附件，它的主件为 q，数量为 cnt[q]，将附件添加到主件后面
                    follows[q][cnt[q]++] = i;
                }
            }
            out.println(compute());
        }
        out.flush();
        out.close();
        br.close();
    }
    public static int compute() {
        Arrays.fill(dp, 0, n + 1, 0);
//        int last = 0; // 上一次计算主件的下标
        for (int i = 1; i <= m; i++) {
            if (kings[i]) { // 只计算主件，在主件后面再视情况选择附件
                for (int j = n; j >= prices[i]; j--) {  // 买得起才考虑
                    dp[j] = Math.max(dp[j], dp[j - prices[i]] + values[i]); // 只考虑主件
                    // 下面考虑附件
                    // 如果有附件，且值考虑附件一
                    if (cnt[i] > 0 && j - prices[i] - prices[follows[i][0]] >= 0) {
                        dp[j] = Math.max(dp[j], dp[j - prices[i] - prices[follows[i][0]]] + values[i] + values[follows[i][0]]);
                    }
                    // 如果有两个附件，且只考虑附件二
                    if (cnt[i] > 1 && j - prices[i] - prices[follows[i][1]] >= 0) {
                        dp[j] = Math.max(dp[j], dp[j - prices[i] - prices[follows[i][1]]] + values[i] + values[follows[i][1]]);
                    }
                    // 如果有两个附件，两个都考虑
                    if (cnt[i] > 1 && j - prices[i] - prices[follows[i][0]] - prices[follows[i][1]] >= 0) {
                        dp[j] = Math.max(
                                dp[j],
                                dp[j - prices[i] - prices[follows[i][0]] - prices[follows[i][1]]]
                                        + values[i] + values[follows[i][0]] + values[follows[i][1]]
                        );
                    }
                }
            }
        }
        return dp[n];
    }
}
```