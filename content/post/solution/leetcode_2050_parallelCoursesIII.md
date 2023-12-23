---
title: "LeetCode 2050. 并行课程 III"
date: 2023-12-23T15:12:06+08:00
draft: false
math: true

tags: ["LeetCode", "拓扑排序", "树形DP", "DP"]
categories: ""
description: "完成某一个课程的最短时间为完成所有前置课程的最大时间加上本课程的时间。根据课程的前置关系建图，按照拓扑排序求出下一门课程的最短时间，即完成前置课程和下一门课程的最大时间。"
---

> [LeetCode 2050. 并行课程 III](https://leetcode.cn/problems/parallel-courses-iii/)
> 
> [算法讲解060【必备】拓扑排序的扩展技巧](https://www.bilibili.com/video/BV12y4y1F79q/)

### 思路

完成某一个课程的最短时间为完成所有前置课程的最大时间加上本课程的时间。根据课程的前置关系建图，按照拓扑排序求出下一门课程的最短时间，即完成前置课程和下一门课程的最大时间。

### 求解过程

1. 首先通过前置课程关系建图，统计入度。
2. 将所有入度为 0 的点加入队列，这些点的最短完成时间为自身所需的时间。然后开始遍历队列。
3. 按照拓扑排序进行遍历，下一门课程的最小完成时间等于**当前课程的完成时间加上下一门课程的时间，与完成下一门课程的最小时间比较，取较大值**。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(n)$

### 代码

```java
class Solution {
    public int minimumTime(int n, int[][] relations, int[] time) {
        ArrayList<Integer>[] graph = new ArrayList[n + 1];
        for (int i = 1; i <= n; i++) {
            graph[i] = new ArrayList<Integer>();
        }
        int[] indegree = new int[n + 1];
        int[] ans = new int[n + 1];
        for (int[] re : relations) {
            graph[re[0]].add(re[1]);
            indegree[re[1]]++;
        }
        int[] queue = new int[n + 1];
        int h = 0, t = 0;
        for (int i = 1; i <= n; i++) {
            if (indegree[i] == 0) {
                queue[t++] = i;
                ans[i] = time[i - 1];
            }
        }
        int res = 0;
        while (h < t) {
            int from = queue[h++];
            res = Math.max(res, ans[from]);
            for (int to : graph[from]) {
                ans[to] = Math.max(ans[to], ans[from] + time[to - 1]);
                if (--indegree[to] == 0) {
                    queue[t++] = to;
                }
            }
        }
        return res;
    }
}
```