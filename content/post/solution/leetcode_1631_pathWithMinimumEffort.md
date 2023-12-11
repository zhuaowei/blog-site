---
title: "LeetCode 1631. 最小体力消耗路径"
date: 2023-12-11T21:05:37+08:00
draft: false
math: true

tags: ["LeetCode", "图", "并查集", "每日一题"]
categories: ""
description: "2023-12-11 leetcode 每日一题，使用并查集和排序，求到达目的地的最小体力消耗。"
---

> [LeetCode 1631. 最小体力消耗路径](https://leetcode.cn/problems/path-with-minimum-effort/)
>
> 参考题解的方法二：[zerotrac 🌸 - 最小体力消耗路径](https://leetcode.cn/problems/path-with-minimum-effort/solutions/459765/zui-xiao-ti-li-xiao-hao-lu-jing-by-zerotrac2/?envType=daily-question&envId=2023-12-11)

### 思路

体力消耗的计算方式是在 (0,0) -> (n - 1,m - 1) 的路径中，相邻两个节点的最大高度差，跟路径的长度无关。所以要找到最小体力消耗路径，要尽量选择高度差小的边，判断当前边组成的路径能够走到右下角的点。

### 求解过程

首先将所有的边存储起来，同时记录下这条边两端的高度差，然后按照高度差的大小将每条边进行排序。

遍历每一条边，将两端的节点连接起来，判断能够到达右下角。如果不能到达，继续添加边；如果可以到达，返回此时的高度差。

为了判断图中左上角和右下角能否连通，可以使用并查集实现。在开始遍历边之前初始化并查集，然后每添加一条边就判断是否连通。

### 复杂度

- 时间复杂度：$O(n \times m)$
- 空间复杂度：$O(n \times m)$

### 代码

```java
class Solution {
    int[] father;

    public int minimumEffortPath(int[][] heights) {
        int n = heights.length, m = heights[0].length;
        ArrayList<int[]> edges = new ArrayList<>();
        father = new int[n * m];
        initUnionFind();
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                int id = m * i + j;
                if (i > 0) {
                    edges.add(new int[] {id - m, id, Math.abs(heights[i][j] - heights[i - 1][j])});
                }
                if (j > 0) {
                    edges.add(new int[] {id - 1, id, Math.abs(heights[i][j] - heights[i][j - 1])});
                }
            }
        }
        // 按照差值排序
        Collections.sort(edges, (e1, e2) -> e1[2] - e2[2]);
        for (int[] edge : edges) {
            unite(edge[0], edge[1]);
            if (isSameSet(0, n * m - 1)) {
                return edge[2];
            }
        }
        return 0;
    }
    public void initUnionFind() {
        for (int i = 0; i < father.length; i++) {
            father[i] = i;
        }
    }
    public void unite(int a, int b) {
        int fa = find(a); 
        int fb = find(b);
        if (fa == fb) {
            return;
        }
        father[fa] = fb;
    }
    public boolean isSameSet(int a, int b) {
        int fa = find(a); 
        int fb = find(b);
        return fa == fb;
    }
    public int find(int x) {
        if (x == father[x]) {
            return x;
        } else {
            father[x] = find(father[x]);
            return father[x];
        }
    }
}
```