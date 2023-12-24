---
title: "LeetCode 543. 二叉树的直径"
date: 2023-12-23T20:53:06+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "树型DP"]
categories: ""
description: "求当前节点的最大直径时，分为经过当前节点和不经过当前节点，因此需要记录下左右子树的最大直径和最大高度，求出两种可能性的最大值。"
---

> [LeetCode 543. 二叉树的直径](https://leetcode.cn/problems/diameter-of-binary-tree/)
>
> [算法讲解079【必备】树型dp-下](https://www.bilibili.com/video/BV1ae411f7AC/)

### 思路

求当前节点的最大直径时，分为经过当前节点和不经过当前节点，因此需要记录下左右子树的最大直径和最大高度，求出两种可能性的最大值。

### 求解过程

求当前节点的最大直径时，需要用到左右子树的信息，所以需要用到二叉树的后续遍历。

1. 当前节点为空树时：节点最大高度为 0，树的最大直径为 0。
2. 当前节点非空时：

    a. 如果经过该节点：最大直径为 左树最大高度 + 右树最大高度。

    b. 如果不经过该节点：最大直径为 左树最大直径和右树最大直径的最大值。

因为直径为两节点之间经过的边数，而不是节点数，所以求经过当前节点的最大直径时，不需要再加一。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(n)$

### 代码

```java
class Solution {
    class Info {
        int height;
        int diameter;
        Info(int h, int d) {
            height = h;
            diameter = d;
        }
    }    
    public int diameterOfBinaryTree(TreeNode root) {
        return process(root).diameter;
    }
    public Info process(TreeNode node) {
        if (node == null) {
            return new Info(0, 0);
        }
        Info infoLeft = process(node.left);
        Info infoRight = process(node.right);
        int height = 1 + Math.max(infoLeft.height, infoRight.height);
        int diameter = Math.max(Math.max(infoLeft.diameter, infoRight.diameter), infoLeft.height + infoRight.height);
        return new Info(height, diameter);
    }
}
```