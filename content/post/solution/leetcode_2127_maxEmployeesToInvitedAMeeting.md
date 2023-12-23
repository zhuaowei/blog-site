---
title: "LeetCode 2127. 参加会议的最多员工数"
date: 2023-12-23T15:52:28+08:00
draft: false
math: true

tags: ["LeetCode", "拓扑排序", "DP", "树形DP", "内向基环树"]
categories: ""
description: "根据题意，一共 n 个点，n 条边，并且每一个点出发只有一条边，说明该图肯定存在环。根据环的大小分为大环和小环，也就有了两种安排座位的方案，分别计算两种方案的人数，取较大者。"
---

> [LeetCode 2127. 参加会议的最多员工数](https://leetcode.cn/problems/maximum-employees-to-be-invited-to-a-meeting/)
>
> [算法讲解060【必备】拓扑排序的扩展技巧](https://www.bilibili.com/video/BV12y4y1F79q/)

### 思路

根据题意，一共 n 个点，n 条边，并且每一个点出发只有一条边，说明该图肯定存在环。根据环的大小分为大环和小环，也就有了两种安排座位的方案，分别计算两种方案的人数，取较大者。

> n 个点和 n 条边组成的图就是基环数。如果每个点只有一条出边，则看起来是向内的，这种也叫内向基环树。如果每个点只有一条入边，则它为外向基环数。

### 求解过程

**两种方案**

根据题意可知，图中存在环，根据环的大小将环分为两种：

1. 小环（size == 2）：遇到这种环，可以现将环安排到座位上，然后再将环上两个节点延伸出来的链安排到座位上。根据贪心原则，我们要尽量选择链长的安排座位。同时，因为座位上原本是链上的点都指向环的节点，因此多个小环的安排方案并不冲突，可以都安排到座位上。
2. 大环（size >= 3）：只能将环上的人安排到座位，且这些座位上的点是收尾相接的，无法插入其他点，所以只能选择最大的环。

最终得到两种方案可以安排的最大人数，取最大值。

**环的计算**

1. 首先按照拓扑排序，统计出每个环山的点，不算自己，可以延伸的最长的链是多长。
2. 经历过拓扑排序后，只剩下环上的点入度不为 0，所以依次遍历所有点，判断入度是否为 0。
3. 如果入度不为 0，从该点出发，遍历环的一周，计算出环的大小。（为了防止重复计算，每经过一个点，将它的入度减一）
4. 根据环的大小和上面的两种方案，分别计算出两种方案的人数，最终返回较大者。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(n)$

### 代码

```java
class Solution {
    public int maximumInvitations(int[] favorite) {
        int n = favorite.length;
        int[] indegree = new int[n];
        int[] queue = new int[n];
        int[] cnt = new int[n];
        int h = 0, t = 0;
        for (int v : favorite) {
            indegree[v]++;
        }
        for (int i = 0; i < n; i++) {
            if (indegree[i] == 0) {
                queue[t++] = i;
            }
        }
        while (h < t) {
            int u = queue[h++];
            int v = favorite[u];
            cnt[v] = Math.max(cnt[v], 1 + cnt[u]);
            if (--indegree[v] == 0) {
                queue[t++] = v;
            }
        }
        // 将有向图的枝通过拓扑排序全部剪掉，只剩下环
        // 如果环的大小为 2，则为这两点连着的最长的链之和再加上 2，多个小环可以一起坐下
        // 如果环的大小>= 3，则只能坐下这个环内的人数，其他人无法插入。
        int small = 0, big = 0; // 分别计算大环和小环的方案最大人数。
        for (int i = 0; i < n; i++) {
            if (indegree[i] != 0) {
                int size = 0, start = i;
                while (indegree[start] != 0) {
                    indegree[start]--;
                    size++;
                    start = favorite[start];
                }
                // 判断环的大小
                if (size == 2) {
                    // 环为 2 时，可以将多组放到一个桌子
                    small += cnt[i] + cnt[favorite[i]] + 2;
                } else {
                    big = Math.max(big, size);
                }
            }
        }
        return Math.max(small, big);
    }
}
```