---
title: "牛客网 【模板】拓扑排序"
date: 2023-12-20T22:47:07+08:00
draft: false
math: true

tags: ["牛客网", "拓扑排序", "链式前向星"]
categories: ""
description: "使用链式前向星方式建图，实现图的拓扑排序。统计节点的入度，将入度为 0 的点加入队列，然后依次出队，将该节点影响的节点入度减一，如果入度为 0，也添加到队列，直到不存在点的入度都为 0。"
---

> [牛客网 【模板】拓扑排序](https://www.nowcoder.com/practice/88f7e156ca7d43a1a535f619cd3f495c)
>
> [算法讲解059【必备】建图、链式前向星、拓扑排序](https://www.bilibili.com/video/BV1rj411k7tS/)

### 思路

首先使用链式前向星建图，同时统计点的入度。然后遍历所有节点，将入度为 0 的点加入队列，遍历队列的每一个点，消除出队节点的影响，将消除影响后入度为 0 的点加入队列，直到不存在入度为 0 的点。

### 求解过程

具体的求解过程可以参考: [LeetCode 2276. 统计区间中的整数数目](/article/leetcode-2276.-统计区间中的整数数目/)

**注意**

一般是竞赛的时候使用链式前向星，而这个时候要重复使用开辟的空间，就需要对上次使用过的数组进行清空。清空是只需要清空 head数组和 `indegree` 数组，并将 cnt 设置为 1。

因为 `head` 数组默认应该存储的是 0，表示该节点已经没有边了，所以要清空，`indegree` 在建图时是自增，需要重置。而 `next` 数组和 `to` 数组，在建图的过程中会覆盖已有的数据，所以可以不用重置为 0，只需要将 cnt 设置为 1 即可，图的编号重新从 1 开始。

### 复杂度

- 时间复杂度：$O(m)$
- 空间复杂度：$O(n + m)$

### 代码

```java
import java.io.*;
import java.util.Arrays;

public class Main {
    public static int MAXN = 200001;
    public static int MAXM = 200001;
    public static int[] head = new int[MAXN];
//    public static int[] weight = new int[MAXN];
    public static int[] next = new int[MAXM];
    public static int[] to = new int[MAXM];
    public static int[] indegree = new int[MAXN];
    public static int[] queue = new int[MAXN];
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
            if (compute()) {
                for (int i = 0; i < t - 1; i++) {
                    out.print(queue[i] + " ");
                }
                out.println(queue[t - 1]);
            } else {
                out.println(-1);
            }
        }
        out.flush();
        out.close();
        br.close();
    }
    public static void init() {
        Arrays.fill(head, 0, n + 1, 0);
        Arrays.fill(indegree, 0, n + 1, 0);
        cnt = 1;
        h = t = 0;
    }
    // 将 u -> v 这条边建出来
    public static void addEdge(int u, int v) {
        next[cnt] = head[u];
        to[cnt] = v;
        // weight[cnt] = w // 权值
        head[u] = cnt++;
    }
    public static boolean compute() {
        // 返回是否能完成拓扑排序
        for (int i = 1; i <= n; i++) {
            if (indegree[i] == 0) {
                queue[t++] = i;
            }
        }
        while (h < t) {
            int node = queue[h++];
            for (int e = head[node]; e != 0; e = next[e]) {
                if (--indegree[to[e]] == 0) {
                    queue[t++] = to[e];
                }
            }
        }
        return t == n;
    }
}

```