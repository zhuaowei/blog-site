---
title: "LeetCode 2458. 移除子树后的二叉树高度"
date: 2023-12-27T22:48:35+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "树型DP", "DFN"]
categories: ""
description: "首先构建出二叉树的 DFN序，记录下每一个节点的树大小和深度。查询时，根据当前节点的DFN序和子树大小，计算出除了当前树区域之外的最大高度。"
---

> [LeetCode 2458. 移除子树后的二叉树高度](https://leetcode.cn/problems/height-of-binary-tree-after-subtree-removal-queries/)
>
> [算法讲解079【必备】树型dp-下](https://www.bilibili.com/video/BV1ae411f7AC/)

### 思路

首先构建出二叉树的 DFN序，记录下每一个节点的树大小和深度。查询时，根据当前节点的DFN序和子树大小，计算出除了当前树区域之外的最大高度。

### 求解过程

**DFN序**

DFN序是指按照遍历顺序重新给树的节点进行编号，同时记录下每个节点的树的大小。根据DFN序和子树大小，可以判断出两个节点是否同属于一棵子树。

使用深度优先遍历进行构建DFN序，每来到一个节点时，先给当前节点进行编号，然后依次递归执行左树和右树，得到左树和右树的大小，计算出本节点的树的大小。本题除了要记录DFN序和 size 数组外，还需要计算出当前节点到头节点的深度 `deep`。

**求最大高度**

例如当前查询的是移除 `i` 为头的子树后的最大高度，它的树大小为 `j`，那么我们就要查询 `deep` 数组在 `[1, i)` 和 `[i + j, n]` 区间的最大值。为了减少查询时间，我们处理两个前缀、后缀数组，分别记录下 `[1, i]` 和 `[i, n]` 的最大深度。

### 复杂度

- 时间复杂度：$O(n + m)$

n 为树的节点个数，m 为查询的次数。

- 空间复杂度：$O(n)$

### 代码

```java
class Solution {
    int MAXN = 100010;
    int[] height = new int[MAXN]; // 距离头部的高度
    int[] dfn = new int[MAXN];  // dfn 序号 index 代表节点值， dfn[index] 代表序号
    int[] size = new int[MAXN]; // 树的大小
    int[] left = new int[MAXN];
    int[] right = new int[MAXN];
    int cnt = 0;
    public int[] treeQueries(TreeNode root, int[] queries) {
        int n = queries.length;
        // 构建DFN序
        dfs(root, 0);
        // 处理前缀 后缀数组
        for (int i = 1; i <= cnt; i++) {
            left[i] = Math.max(left[i - 1], height[i]);
            right[cnt + 1 - i] = Math.max(right[cnt + 2 - i], height[cnt + 1 - i]);
        }
        // 查询
        for (int i = 0; i < n; i++) {
            int index = dfn[queries[i]];
            int s = size[index];
            queries[i] = Math.max(left[index - 1], right[index + s]);
        }
        return queries;
    }
    public int dfs(TreeNode node, int deep) {
        int index = ++cnt;
        dfn[node.val] = index;
        height[index] = deep;
        size[index] = 1;
        if (node.left != null) {
            size[index] += dfs(node.left, deep + 1);
        }
        if (node.right != null) {
            size[index] += dfs(node.right, deep + 1);
        }
        return size[index];
    }
}
```