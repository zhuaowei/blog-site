---
title: "LeetCode 337. 打家劫舍 III"
date: 2023-12-23T21:33:14+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "树型DP"]
categories: ""
description: "先求出左右子节点可以在偷与不偷时可以获取的最大金额，然后根据子节点的信息汇总出当前节点偷与不偷的最大金额。"
---

> [LeetCode 337. 打家劫舍 III](https://leetcode.cn/problems/house-robber-iii/)
>
> [算法讲解079【必备】树型dp-下](https://www.bilibili.com/video/BV1ae411f7AC/)

### 思路

先求出左右子节点可以在偷与不偷时可以获取的最大金额，然后根据子节点的信息汇总出当前节点偷与不偷的最大金额。

### 求解过程

在求出本节点的信息之前，需要先获取左右子节点的信息，所以需要使用二叉树的后序遍历。

1. 如果当前节点是空节点，那么偷与不偷的金额都是 0。
2. 如果非空，则先递归求出子节点的信息，当期节点偷与不偷分别讨论：

    a. 如果当前节点不偷，那么在左右子节点中分别选择偷与不偷的最大值进行相加。

    b. 如果当前节点偷，那么在左右子节点中选择不偷的值相加，再加上本节点的值。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(n)$

### 代码

```java
class Solution {
    public int rob(TreeNode root) {
        int[] ans = process(root);
        return Math.max(ans[0], ans[1]);
    }
    public int[] process(TreeNode node) {
        int[] ans = new int[2];
        if (node == null) {
            return ans;
        }
        int[] LInfo = process(node.left);
        int[] RInfo = process(node.right);
        // 当前不能偷，就在左右孩子中挑最大的。
        ans[0] = Math.max(LInfo[0], LInfo[1]) + Math.max(RInfo[0], RInfo[1]);
        ans[1] = LInfo[0] + RInfo[0] + node.val;
        return ans;
    }
}
```