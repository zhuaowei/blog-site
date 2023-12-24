---
title: "洛谷 P4017 最大食物链计数"
date: 2023-12-22T22:40:29+08:00
draft: false
math: true

tags: ["洛谷", "链式前向星", "拓扑排序", "DP", "树型DP"]
categories: ""
description: "到达某生物的食物链条数等于所有被他吃的生物的链数之和。生物链的关系可以看作一个有向无环图，按照拓扑排序将食物链条数从被捕食者传递到捕食者，最终得到整个生物捕食关系的食物链总数。"
---

> [洛谷 P4017 最大食物链计数](https://www.luogu.com.cn/problem/P4017)
>
> [算法讲解060【必备】拓扑排序的扩展技巧](https://www.bilibili.com/video/BV12y4y1F79q/)

### 思路

到达某生物的食物链条数等于所有被他吃的生物的链数之和。生物链的关系可以看作一个有向无环图，按照拓扑排序将食物链条数从被捕食者传递到捕食者，最终得到整个生物捕食关系的食物链总数。

### 求解过程

1. 首先建图，使用链式前向星法。同时统计每个节点的入度，用于拓扑排序。
2. 如果遍历所有节点，将入度为 0 的点加入队列，这些店都是处于食物链最底层的生物，设置他们的食物链条数为 1.
3. 按照拓扑排序遍历队列中的点。如果队列中的点没有后续节点了，说明它是处于食物链顶层的生物，累计到生物链条数中。如果还有节点，按照拓扑排序的规则减少目的点入度，将入度为 0 的节点添加到队列。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(n)$

### 代码

```java
import java.io.*;
import java.util.Arrays;

public class Main {
    public static int MAXN = 5001;
    public static int MAXM = 500001;
    public static int MOD = 80112002;
    public static int[] head = new int[MAXN];
    public static int[] next = new int[MAXM];
    public static int[] to = new int[MAXM];
    public static int[] indegree = new int[MAXN];
    public static int[] queue = new int[MAXN];
    public static int[] ans = new int[MAXN];
    public static int cnt, h, t, n, m;
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            n = (int)in.nval; in.nextToken();
            m = (int)in.nval;
            // 初始化
            init();
            for (int i = 1, u, v; i <= m; i++) {
                in.nextToken(); u = (int)in.nval;
                in.nextToken(); v = (int)in.nval;
                addEdge(u, v);
                indegree[v]++;
            }
            out.println(compute());
        }
        out.flush();
        out.close();
        br.close();
    }
    public static void init() {
        Arrays.fill(head, 0, n + 1, 0);
        Arrays.fill(indegree, 0, n + 1, 0);
        Arrays.fill(ans, 0, n + 1, 0);
        cnt = 1;
        h = t = 0;
    }
    public static void addEdge(int u, int v) {
        next[cnt] = head[u];
        to[cnt] = v;
        head[u] = cnt++;
    }
    // 食物链不会存在环，也就是拓扑排序的后节点个数等于 n
    public static int compute() {
        for (int i = 1; i <= n; i++) {
            if (indegree[i] == 0) {
                queue[t++] = i;
                // i 处于食物链最底层，初始化食物链条数为 1
                ans[i] = 1;
            }
        }
        int u, v, res = 0;
        while (h < t) {
            u = queue[h++];
            if (head[u] == 0) {
                // 到达食物链顶层，累计结果
                res = (res + ans[u]) % MOD;
            } else {
                // 否则继续遍历
                for (int e = head[u]; e != 0; e = next[e]) {
                    v = to[e];
                    ans[v] = (ans[u] + ans[v]) % MOD;
                    if (--indegree[v] == 0) {
                        queue[t++] = v;
                    }
                }
            }
        }
        return res;
    }
}
```