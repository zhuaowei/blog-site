---
title: "LeetCode 2246. 相邻字符不同的最长路径"
date: 2023-12-27T22:45:50+08:00
draft: false
math: true

tags: ["LeetCode", "图", "DFS", "DP", "树型DP"]
categories: ""
description: "定义子问题：求子树的相邻字符不同的最长路径。路径分为经过和不经过当前树的头节点两种，分别计算两种路径长度，取最大值。"
---

> [LeetCode 2246. 相邻字符不同的最长路径](https://leetcode.cn/problems/longest-path-with-different-adjacent-characters/)
>
> [算法讲解079【必备】树型dp-下](https://www.bilibili.com/video/BV1ae411f7AC/)

### 思路

定义子问题：求子树的相邻字符不同的最长路径。路径分为经过和不经过当前树的头节点两种，分别计算两种路径长度，取最大值。

### 求解过程

子问题分为经过和不经过当前树的头节点两种。

1. 经过当前头节点的路径，需要在头节点的所有子节点为头的树中，找出与头节点字符不相等的最长和次长树高度，路径长度为两个树高之和再加 1。
2. 不经过当前头节点的路径，需要在头节点的所有子节点为头的树中，找出最长的路径。

因此，需要按照前序遍历的方式，遍历整个树，每个子节点给父节点返回两个信息，当前树的最长相邻字符不等的高度，以及当前树的最长路径。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(n)$

### 代码

```java
class Solution {
    class Info {
        int height;
        int length;
        Info(int h, int l) {
            height = h;
            length = l;
        }
    }
    public int longestPath(int[] parent, String s) {
        int n = parent.length;
        char[] str = s.toCharArray();
        List<List<Integer>> graph = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            graph.add(new ArrayList<Integer>());
        }
        for (int i = 1; i < n; i++) {
            graph.get(parent[i]).add(i);
        }
        return process(graph, str, 0).length;
    }
    public Info process(List<List<Integer>> graph, char[] str, int node) {
        int h = 1, l = 1;
        int max1 = 0, max2 = 0;
        for (int next : graph.get(node)) {
            Info info = process(graph, str, next);
            // 不经过头节点的最长路径
            l = Math.max(l, info.length);
            // 求树的最长和次长高度
            if (str[node] != str[next]) {
                if (info.height > max1) {
                    max2 = max1;
                    max1 = info.height;
                } else if (info.height > max2) {
                    max2 = info.height;
                }
            }
        }
        // 经过和不经过，取最大值
        l = Math.max(l, 1 + max1 + max2);
        return new Info(h + max1, l);
    }
}
```