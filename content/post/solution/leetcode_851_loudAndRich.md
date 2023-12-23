---
title: "LeetCode 851. 喧闹和富有"
date: 2023-12-22T23:13:31+08:00
draft: false
math: true

tags: ["LeetCode", "拓扑排序", "DP", "树形DP"]
categories: ""
description: "根据输入的财富关系，我们可以得到一个财富关系图，按照拓扑排序从最有钱的依次遍历，比较前后两人的安静值，得到更符合要求的答案。"
---

> [LeetCode 851. 喧闹和富有](https://leetcode.cn/problems/loud-and-rich/)
> 
> [算法讲解060【必备】拓扑排序的扩展技巧](https://www.bilibili.com/video/BV12y4y1F79q/)

### 思路

根据输入的财富关系，我们可以得到一个财富关系图，按照拓扑排序从最有钱的依次遍历，比较前后两人的安静值，得到更符合要求的答案。

### 求解过程

1. 首先建图，使用邻接表法，统计节点的入度。同时准备好结果数组，将默认值设置为当前节点。
2. 使用拓扑排序遍历节点，将出发节点与目标节点的安静值进行比较，将更安静的节点赋值目标节点。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(n)$

### 代码

```java
class Solution {
    public int[] loudAndRich(int[][] richer, int[] quiet) {
        int n = quiet.length;
        int[] ans = new int[n];
        int[] indegree = new int[n];
        int[] queue = new int[n];
        int h = 0, t = 0;
        ArrayList<Integer>[] graph = new ArrayList[n];
        for (int i = 0; i < n; i++) {
            graph[i] = new ArrayList<Integer>();
        }
        for (int[] edge : richer) {
            graph[edge[0]].add(edge[1]);
            indegree[edge[1]]++;
        }
        // 拓扑排序
        for (int i = 0; i < n; i++) {
            if (indegree[i] == 0) {
                queue[t++] = i;
            }
            ans[i] = i;
        }
        while (h < t) {
            int from = queue[h++];
            for (int to : graph[from]) {
                // 如果出发点比目的点更安静，赋值给目的点。
                if (quiet[ans[to]] > quiet[ans[from]]) {
                    ans[to] = ans[from];
                }
                if (--indegree[to] == 0) {
                    queue[t++] = to;
                }
            }
        }
        return ans;
    }
}
```