---
title: "LeetCode 1466. 重新规划路线"
date: 2023-12-07T22:40:29+08:00
draft: false
math: true

tags: ["LeetCode", "图", "DFS", "每日一题"]
categories: ""
description: "2023-12-07 leetcode 每日一题，使用 DFS 的方式解决"
---

> [1466. 重新规划路线](https://leetcode.cn/problems/reorder-routes-to-make-all-paths-lead-to-the-city-zero/)

## 思路

一共有 n 个点，编号从 0 -> n - 1，一共有 n - 1 条单向边将这些点串起来。从题意可以得知，这是一个有向无环图，节点之间有唯一的一条路径。

两个节点之间是单向边，如果要修改路的放线，使所有点都可以到达 0 号点，需要从 0 号点出发，查看是否每一条边都 “指向 0 号点的方向”。如果没有指向，就修改，计数 +1；否则不做任何操作，继续遍历下一个点。

## 求解过程

因为要从 0 号点出发到达每一个点，所以需要构建一个无相图。同时遍历图的过程中，需要记录边的方向，这里使用节点的入度进行表示。如果当前节点连接的下一个节点有入度，说明**可能是从当前节点指向下一个节点的**。

因为有不确定性，可能是下下个节点指向下一个节点的入度，所以在判断之前，先将下下个节点指向下一个节点的影响消除，如果还剩下入度，则说明是当前节点指向下一个节点的。

从 0 节点开始不断进行递归调用，在到达一个新节点时，先递归调用下一个节点，直到叶子节点。

如果叶子节点指向上一个节点，则不用改变方向，但是需要将上一个节点的入度 -1，这就是消除影响。如果上一个节点指向叶子节点，需要改变方向，不需要消除影响，因为没有影响上一个节点。

就这样不断向上回溯，来到 i 节点时，它的所有子节点的影响都消除了，执行同样的操作逻辑，消除 i 节点的影响。最终返回到 0 节点，得到需要变向的数量。

## 复杂度

- 时间复杂度： $O(n)$ 每个节点只遍历一遍

- 空间复杂度： $O(n)$ 需要一个邻接表和入度表

## 代码

```java []
class Solution {
    public int minReorder(int n, int[][] connections) {
        List<Integer>[] graph = new List[n];
        for (int i = 0; i < n; i++) {
            graph[i] = new ArrayList<Integer>();
        }
        // 初始化入度表和邻接表
        int[] in = new int[n];
        for (int[] con : connections) {
            graph[con[0]].add(con[1]);
            graph[con[1]].add(con[0]);
            in[con[1]]++;
        }
        return process(graph, in, 0, -1);
    }
    // 来到了 index 节点，它的父节点是 fa，
    // 返回从该节点出发到所有子节点的变向数量
    public int process(List<Integer>[] graph, int[] in, int index, int fa) {
        int cnt = 0;
        for (int node : graph[index]) {
            if (node != fa) {
                // 先进行递归，在下一个递归函数中会消除影响
                cnt += process(graph, in, node, index);
                // 现在 node 下边节点的影响已经消除
                if (in[node] > 0) {
                    // index -> node 变向
                    cnt++;
                    in[node]--;
                } else {
                    // 消除 index <- node 的影响
                    in[index]--;
                }
            }
        }
        return cnt;
    }
}
```
