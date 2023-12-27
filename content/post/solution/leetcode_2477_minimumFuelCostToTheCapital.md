---
title: "LeetCode 2477. 到达首都的最少油耗"
date: 2023-12-05T11:07:39+08:00
draft: false
math: true

tags: ["LeetCode", "图", "DFS", "DP", "树型DP", "每日一题"]
categories: ""
description: "2023-12-05 leetcode 每日一题，使用 DFS 在递归过程中动态调整乘坐车辆的数量，达到油耗最低。"
---

> [2477. 到达首都的最少油耗](https://leetcode.cn/problems/minimum-fuel-cost-to-report-to-the-capital/)

## 思路

一共有 n 的点，编号 0 -> n - 1，使用 n - 1 条边进行连接，所以整个图是无环的，并且任意两个节点之间的路径是唯一的。

一开始的思路是 求出每个叶子节点到达首都的路径，对于每一条路径，根据节点数就可以计算出油耗，最终相加。后来发现是错误的，因为行驶过程中可以下车换乘。也就是说原本可以坐 3 个人的车，从 2 个节点来到 i ，他们每人都一辆车，到达 i 节点后，算上 i 节点上的人，一共有 3 个人，他们坐一辆车就够了，而不是分别计算油耗。

所以需要从首都开始递归，查看到达每一个中间节点的人数，然后根据人数重新安排车辆。

## 求解过程

首先使用邻接表建出来整个图，用于递归遍历。然后从 0 开始进行递归。

假如当前到达了 i 号点，它的子节点是 node ，先进行子过程的调用，返回 node 节点一共有多少人，根据人数计算出这些人从 node 到达 i 点的油耗，再将人数计算到 i 号点的人数中去。计算出所有子节点的人数和油耗之后，就可以将人数返回。

当递归调用来到了叶子节点时，因为只有一条路径，叶子节点没有子节点可以遍历，整个 for 循环相当于直接跳过，最终只返回叶子节点的人数。

## 复杂度

- 时间复杂度：$O(n)$

遍历一遍图即可，每个节点进入函数一次，出函数一次。

- 空间复杂度：$O(n)$

需要将图建出来，邻接表。

## 代码

```java
class Solution {
    long ans = 0;

    public long minimumFuelCost(int[][] roads, int seats) {
        // n - 1 条边，一共有 n 个节点
        int n = roads.length + 1;
        List<Integer>[] graph = new List[n];
        for (int i = 0; i < n; i++) {
            graph[i] = new ArrayList<Integer>();
        }
        for (int[] road : roads) {
            graph[road[0]].add(road[1]);
            graph[road[1]].add(road[0]);
        }
        dfs(graph, 0, -1, seats);
        return ans;
    }
    // 返回到达 index 点，一共有多少人。
    public int dfs(List<Integer>[] graph, int index, int fa, int seats) {
        // 到达 index 点至少有 1 个人(当前节点)
        int num = 1, nextNum;
        for (int node : graph[index]) {
            if (node != fa) {
                // 从 下一个节点来的人有多少。
                nextNum = dfs(graph, node, index, seats);
                // nextNum 个人从 node 点到达 index 点的耗油
                ans += (nextNum + seats - 1) / seats;
                num += nextNum;
            }
        }
        return num;
    }
}
```