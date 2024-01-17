---
title: "LeetCode 2322. 从树中删除边的最小分数"
date: 2024-01-05T10:57:32+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "树型DP", "DFN"]
categories: ""
description: "遍历整棵树，构建出DFN序和size数组，同时计算出每个子树的异或和。然后遍历任意两条边的组合，求出最小分数。"
---

> [LeetCode 2322. 从树中删除边的最小分数](https://leetcode.cn/problems/minimum-score-after-removals-on-a-tree/)
>
> [算法讲解079【必备】树型dp-下](https://www.bilibili.com/video/BV1ae411f7AC/)

### 思路

遍历整棵树，构建出DFN序和size数组，同时计算出每个子树的异或和。然后遍历任意两条边的组合，求出最小分数。

### 求解过程

**两条边的关系**

无论以那个节点为整个树的头节点，同样的两条边划分的子集是一样的，所以遍历时就按照以 0 为头节点。

两条边的的位置关系有以下两种：一是并列，二是包含。先删除一条边，将整个树划分为头树和子树两部分，并列指第二条边在头树这部分，包含指第二条边在子树这部分。根据不同的情况选择不同的异或和计算方式。

为了区分两条边的情况，因此需要使用DFN序和子树大小判断两个节点是否在同一棵树上。同时还要计算出子树的异或和，便于计算划分后的子集的异或和。

**计算过程**

首先构建出无向图，从 0 号节点还是递归，计算出DFN序、子树大小size、子树异或和。

然后两层for循环遍历每一种组合情况。划分子集时，要求出划分后的子树的头节点，因此对于边上的两个点，取出远离 0 号节点的那一个，也就是DFN序更大的一个。

求出划分后两个子集的的头节点后，判断DFN序较大的节点是否在较小的节点为头的树上，区分出并列还是包含的关系。计算出三个子集的异或和，计算出分数，取最小分数。

### 复杂度

- 时间复杂度：$O(n + m^2)$

n 为节点个数，m 为边的个数。

- 空间复杂度：$O(n)$

### 代码

```java
class Solution {
    int MAXN = 1005;
    int[] xor = new int[MAXN];
    int[] dfn = new int[MAXN];
    int[] size = new int[MAXN];
    int cnt = 0;
    public int minimumScore(int[] nums, int[][] edges) {
        int n = nums.length;
        List<List<Integer>> graph = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            graph.add(new ArrayList<Integer>());
        }
        for (int[] edge: edges) {
            graph.get(edge[0]).add(edge[1]);
            graph.get(edge[1]).add(edge[0]);
        }
        dfs(graph, nums, 0);
        int m = edges.length;
        int ans = Integer.MAX_VALUE;
        // 遍历任意两条边
        for (int i = 0, a, b, pre, pos, sum1, sum2, sum3; i < m; i++) {
            // a b 分别是两条边中，远离 0 节点的点。
            a = Math.max(dfn[edges[i][0]], dfn[edges[i][1]]);
            for (int j = i + 1; j < m; j++) {
                b = Math.max(dfn[edges[j][0]], dfn[edges[j][1]]);
                // 调换 a b 的位置，确定前后顺序
                if (a < b) {
                    pre = a;
                    pos = b;
                } else {
                    pre = b;
                    pos = a;
                }
                // 后一个树，它的异或值是确定的
                sum1 = xor[pos];
                if (pos < pre + size[pre]) {
                    // 如果是包含关系
                    sum2 = xor[pre] ^ xor[pos];
                    sum3 = xor[1] ^ xor[pre];
                } else {
                    sum2 = xor[pre];
                    sum3 = xor[1] ^ xor[pre] ^ xor[pos];
                }
                // 结算
                int max = Math.max(sum1, Math.max(sum2, sum3));
                int min = Math.min(sum1, Math.min(sum2, sum3));
                ans = Math.min(ans, max - min);
            }
        }
        return ans;
    }
    public void dfs(List<List<Integer>> graph, int[] nums, int node) {
        int i = ++cnt;
        dfn[node] = i;
        size[i] = 1;
        xor[i] = nums[node];
        for (int next : graph.get(node)) {
            if (dfn[next] == 0) {
                dfs(graph, nums, next);
                xor[i] ^= xor[dfn[next]];
                size[i] += size[dfn[next]];
            }
        }
    }
}
```