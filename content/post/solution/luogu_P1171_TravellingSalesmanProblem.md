---
title: "洛谷 P1171 售货员的难题"
date: 2024-01-18T22:47:58+08:00
draft: false
math: true

tags: ["洛谷", "DP", "状压DP", "TSP"]
categories: ""
description: "使用一个状态变量表示村庄是否已经经过，每次来到一个村庄时，考虑所有没有去过的村庄作为下一站，在所有村庄中，选择路程最小的。"
---

> [洛谷 P1171 售货员的难题](https://www.luogu.com.cn/problem/P1171)
>
> [算法讲解080【必备】状压dp-上](https://www.bilibili.com/video/BV15a4y1o7NA/)

### 思路

所有村庄中，任意两个村庄之间都是连通的，所以售货员的路线可能是任意一条，我们要枚举出每一条行驶路线，找到路程最短的那条经过的路程是多少。我们使用一个状态变量表示村庄是否已经经过，每次来到一个村庄时，考虑所有没有去过的村庄作为下一站，在所有村庄中，选择路程最小的。

### 求解过程

**状态压缩**

最多有 20 个村庄，所以可以使用一个 `int` 类型的整型变量表示所有村庄的状态。在递归过程中还需要一个 dp 表记录递归的结果，以便于在重复调用时再次递归展开。只使用村庄的状态作为条件无法确定是否是同一个递归，还需要知道当前在哪个村庄，因为只要知道了当前位置和村庄的状态，那么经过的村庄的最短路径是确定的。例如当前在 5 村庄，递归最底层是要回到 0 村庄（以 0 村庄作为出发点），那么从 0 -> 5 的最短路径是确定的。

**Base Case**

当所有的村庄都经过一遍之后，要回到初始的位置（村庄 0），返回当前村庄到达村庄0 的路程。

**递归调用**

假设当前来到了村庄 i，枚举每一个没有去过的村庄 j，从 i 出发走完一遍剩余村庄再回到村庄 0 的最短总路程为 

$$
\min(distance_{ij} + process(j, status | (1 << j)))
$$

**优化**

C++ 可以根据上面的思路编写代码通过，但是 Java 会被卡空间，因此需要对空间进行优化。二维表只记录除出发点之外的所有村庄之间的距离，出发点到各村庄和各村庄到出发点的距离使用两个一维表记录。递归开始村庄从除出发点之外的所有村庄开始，将得到的结果加上从出发点到达各村庄的距离，将所有结果进行比较，取最小值。

### 复杂度

- 时间复杂度：$O(n \times 2 ^ n)$
- 空间复杂度：$O(n \times 2 ^ n)$

### 代码

```java
import java.io.*;

public class Main {
    public static int MAXN = 19;
    public static int[][] graph = new int[MAXN][MAXN];
    public static int[] start = new int[MAXN];
    public static int[] back = new int[MAXN];
    public static int[][] dp = new int[1 << MAXN][MAXN];
    public static int n;

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int)in.nval - 1;
            in.nextToken(); // 0,0 位置舍弃
            for (int i = 0; i < n; i++) {
                in.nextToken();
                start[i] = (int)in.nval;
            }
            for (int i = 0; i < n; i++) {
                in.nextToken();
                back[i] = (int)in.nval;
                for (int j = 0; j < n; j++) {
                    in.nextToken();
                    graph[i][j] = (int)in.nval;
                }
            }
            out.println(compute());
        }
        out.flush();
        out.close();
        br.close();
    }
    public static int compute() {
        for (int i = 0; i < (1 << n); i++) {
            for (int j = 0; j < n; j++) {
                dp[i][j] = -1;
            }
        }
        int ans = Integer.MAX_VALUE;
        for (int i = 0; i < n; i++) {
            // 从各个村庄出发，再加上从出发点到各个村庄的距离
            ans = Math.min(ans, start[i] + process(1 << i, i));
        }
        return ans;
    }

    public static int process(int status, int cur) {
        if (status == (1 << n) - 1) {
            // 从当前村庄回到出发点
            return back[cur];
        }
        if (dp[status][cur] != -1) {
            return dp[status][cur];
        }
        int ans = Integer.MAX_VALUE;
        for (int i = 0; i < n; i++) {
            if ((status & (1 << i)) == 0) {
                // 所有路程取最小值
                ans = Math.min(ans, graph[cur][i] + process(status | (1 << i), i));
            }
        }
        dp[status][cur] = ans;
        return ans;
    }
}

```