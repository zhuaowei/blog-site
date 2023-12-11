---
title: "LeetCode ByteDance-006. 夏季特惠"
date: 2023-12-10T17:00:24+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "背包问题", "01背包"]
categories: ""
description: "01 背包问题的转化，需要根据优惠调整自己的预期，使用最终的预期对待定的游戏进行01 背包DP。"
---

> [LeetCode ByteDance-006. 夏季特惠](https://leetcode.cn/problems/tJau2o/)
> 
> [算法讲解073【必备】背包dp-01背包、有依赖的背包](https://www.bilibili.com/list/)

### 思路

需要分析题意，将问题转化为 01 背包问题。这题背包的容量不是确定的预算，需要根据优惠决定。

游戏有原价和现价，优惠等于原价减现价。优惠越大，心理越不觉得亏，**当优惠比现价还大时，觉得自己赚了**，那么这个游戏就是必选的，赚得多余的还可以用于购买其他游戏。例如原价 10，现价 3，优惠 7，优惠等于现价，四舍五入等于白嫖，还赚 4 块。

所以对于每个游戏，求出自己“赚多少”，如果赚的为正数或者0，则游戏必选；如果赚的为负数，待定，等全部看一遍后，在从待定里选择比较赚的。

### 求解过程

首先，求出每个游戏自己可以“赚多少”，如果赚了，游戏选上；如果每赚，添加到待定。

所有的游戏都遍历一遍后，从待定的游戏中，选择快乐值最高的游戏。此时，因为优惠，自己的预算可能已经被拔高了，最终的预算等于原来的预算 + “自己赚的”。

### 复杂度

- 时间复杂度：$O(N \times M)$

N 为游戏数量，M 为最终被拔高后的预算

- 空间复杂度：$O(M)$

### 代码
```java
import java.io.*;
import java.util.Arrays;

public class Main {
    public static int MAXN = 505;
    // 预算可能被拔高，不是题目上说的 1w
    public static int MAXX = 100005;
    public static int[] costs = new int[MAXN];
    public static long[] values = new long[MAXN];
    public static long[] dp = new long[MAXX];
    public static int n, m, x;
    
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int)in.nval; in.nextToken();
            m = 1;
            x = (int)in.nval;
            long ans = 0, happy = 0;
            for (int i = 0, prev, cur, well; i < n; i++) {
                in.nextToken(); prev = (int)in.nval; 
                in.nextToken(); cur = (int)in.nval; 
                in.nextToken(); happy = (long)in.nval;
                well = prev - cur - cur;
                if (well >= 0) {
                    // 优惠比折后更多，约等于白嫖，自己还能赚点
                    x += well; // 预算被拔高
                    ans += happy; // 快乐值累加
                } else { // 待定
                    costs[m] = -well;
                    values[m++] = happy;
                }
            }
            ans += compute();
            out.println(ans);
        }
        out.flush();
        out.close();
        br.close();
    }
    // 01 背包
    public static long compute() {
        Arrays.fill(dp, 0, x + 1, 0);
        for (int i = 1; i <= m; i++) {
            for (int j = x; j >= costs[i]; j--) {
                dp[j] = Math.max(dp[j], dp[j - costs[i]] + values[i]);
            }
        }
        return dp[x];
    }
}
```