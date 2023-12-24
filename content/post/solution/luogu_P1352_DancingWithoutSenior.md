---
title: "洛谷 P1352 没有上司的舞会"
date: 2023-12-23T22:22:05+08:00
draft: false
math: true

tags: ["洛谷", "链式前向星", "DP", "树型DP"]
categories: ""
description: "求子问题：求出每个树在头节点去与不去的最大快乐值。每棵树的最大快乐值取决于子树返回的信息，根据子树信息求出当前树的信息，不断向上返回，最终得到整棵树的去与不去的最大快乐值。"
---

> [洛谷 P1352 没有上司的舞会](https://www.luogu.com.cn/problem/P1352)
>
> [算法讲解079【必备】树型dp-下](https://www.bilibili.com/video/BV1ae411f7AC/)

### 思路

求子问题：求出每个树在头节点去与不去的最大快乐值。每棵树的最大快乐值取决于子树返回的信息，根据子树信息求出当前树的信息，不断向上返回，最终得到整棵树的去与不去的最大快乐值。

### 求解过程

1. 使用链式前向星建图，求出校长节点。
2. 从校长节点开始往下递归，先求出子树的信息，然后根据子树的信息，求出当前节点去与不去的最大快乐值，一路向上返回，就得到了校长去与不去的最大快乐值。在校长去与不去中，选择快乐值最大的。

    a. 当前节点不去：最大快乐值等于 子树头节点去与不去的最大值进行累加。

    b. 当前节点去：最大快乐值等于 当前节点快乐值加上子树头节点不去的最大快乐值之和。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(n)$

### 代码

```java
import java.io.*;
import java.util.Arrays;

public class Main {
    public static int MAXN = 6001;
    public static int MAXM = 6001;
    public static int[] head = new int[MAXN];
    public static int[] next = new int[MAXM];
    public static int[] to = new int[MAXM];
    public static int[] value = new int[MAXN];
    public static boolean[] boss = new boolean[MAXN];
    public static int[][] ans = new int[MAXN][2];
    public static int cnt, n, principal;
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int)in.nval;
            init();
            Arrays.fill(boss, 1, n + 1, true);
            for (int i = 1; i <= n; i++) {
                in.nextToken(); value[i] = (int)in.nval;
            }
            for (int i = 1, u, v; i <= n - 1; i++) {
                in.nextToken(); v = (int)in.nval;
                in.nextToken(); u = (int)in.nval;
                boss[v] = false;
                addEdge(u, v);
            }
            // 找到校长。
            for (int i = 1; i <= n; i++) {
                if (boss[i]) {
                    principal = i;
                    break;
                }
            }
            compute(principal);
            out.println(Math.max(ans[principal][0], ans[principal][1]));
        }
        out.flush();
        out.close();
        br.close();
    }
    public static void init() {
        Arrays.fill(head, 0, n + 1, 0);
        cnt = 1;
    }
    // 将 u -> v 这条边建出来
    public static void addEdge(int u, int v) {
        next[cnt] = head[u];
        to[cnt] = v;
        head[u] = cnt++;
    }
    public static void compute(int u) {
        ans[u][0] = 0;
        ans[u][1] = value[u];
        for (int e = head[u], v; e != 0; e = next[e]) {
            v = to[e];
            compute(v);
            ans[u][0] += Math.max(ans[v][0], ans[v][1]);
            ans[u][1] += ans[v][0];
        }
    }
}
```