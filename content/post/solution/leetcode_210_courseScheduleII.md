---
title: "LeetCode 210. 课程表 II"
date: 2023-12-20T22:06:49+08:00
draft: false
math: true

tags: ["LeetCode", "链式前向星"]
categories: ""
description: "链式前向星建图法是一种开销较小，空间最大值确定的建图方式，在比赛中很常用，平常刷力扣牛客可以使用邻接表法和邻接矩阵法。本文使用链式前向星建图并实现拓扑排序算法。"
---

> [LeetCode 210. 课程表 II](https://leetcode.cn/problems/course-schedule-ii/)
>
> [算法讲解059【必备】建图、链式前向星、拓扑排序](https://www.bilibili.com/video/BV1rj411k7tS/)

### 思路

建图有很多种方式，常用的有邻接表法和邻接矩阵法。邻接矩阵法在点集很大的情况下很浪费空间，因为它的空间复杂度是 $O(n^2)$ 的。邻接表法在算法开始前是无法确定它的大小的，在比赛中空间计算是累计方式计算的，虽然最后释放了空间，但是还是会计算到。因此需要使用一种开销比较小，同时又知道图用到的最大空间的方式来建图。

### 求解过程

**申请空间**

1. $head$ 数组：大小为节点个数。$head_i$ 表示 $i$ 号节点的对应的边，也就是建图过程中最后一条从 i 出发的边。
2. $next$ 数组：$next_e$ 表示 $e$ 号边的下一条边，也即从 $i$ 号点出发的边中另外一条边。一直遍历 `e = next[e]` 可以遍历从 $i$ 号点出发的每一条边。
3. $to$ 数组：$to_e$ 表示 $e$ 号边的目的节点。
4. $cnt$ ： 一个整数，从 1 开始，表示边的编号。
5. 以上都是必备的空间，如果需要记录边的权重，点的入度出度等，可以再开辟空间。 

因为 $head$ 数组是的索引是节点，所以 $head$ 的大小为节点个数，而 $next$ 和 $to$ 数组，索引表示的是边的编号，所以需要边大小的空间。

**建图**

假设当前建立 `u -> v` 这条边：

1. `next[cnt] = head[u]`: 当前边的编号指向出发节点的上一条边的编号。
2. `to[cnt] = pre[0]`: 记录当前边指向哪一个点。
3. `head[u] = cnt++`: 出发节点指向最新的一条边，边的编号 +1。
4. 以上都是必备数组的建立过程，如果需要添加边权和入度等，可以在合适的位置插入建立步骤。

**拓扑排序**

1. 遍历每一个点，如果发现入度为 0 的点，添加到队列。
2. 遍历队列中的每一个点，直到队列为空。
3. 从队列中取出一个点，遍历每一条从该点出发的边，将从它出发的每一个目的节点的入度 -1，如果目的节点的入度减少后为 0，添加到队列。重复此步骤。

因为使用数组实现队列，且指向队列头部的指针 h 不会后退，添加到队列的节点顺序即为一个满足条件的顺序，所以可以直接把入队顺序作为结果。

### 复杂度

- 时间复杂度：$O(m)$

n 为节点个数，m 为边个数。建图的时间复杂度是 m，拓扑排序的时间复杂度是 m。虽然拓扑排序是两层循环，但是每一条边只被遍历一次，最终所有的边都会遍历一次。

- 空间复杂度：$O(n + m)$

head indegree queue 数组的大小取决于节点的个数；next to 数组的大小取决于边的个数。

### 代码

```java
class Solution {
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        int n = numCourses, m = prerequisites.length;
        int[] head = new int[n];
        int[] next = new int[m + 1];
        int[] to = new int[m + 1];
        int[] indegree = new int[n];
        int[] queue = new int[n];
        int h = 0, r = 0;
        int cnt = 1;
        for (int[] pre : prerequisites) {
            // pre[0] <- pre[1];
            next[cnt] = head[pre[1]];
            to[cnt] = pre[0];
            indegree[pre[0]]++;
            head[pre[1]] = cnt++;
        }
        for (int i = 0; i < n; i++) {
            if (indegree[i] == 0) {
                queue[r++] = i;
            }
        }
        while (h < r) {
            int node = queue[h++];
            for (int e = head[node]; e != 0; e = next[e]) {
                if (--indegree[to[e]] == 0) {
                    queue[r++] = to[e];
                }
            }
        }
        if (r == n) {
            return queue;
        } else {
            return new int[] {};
        }
    }
}
```