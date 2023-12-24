---
title: "LeetCode 1373. 二叉搜索子树的最大键值和"
date: 2023-12-23T20:06:43+08:00
draft: true
math: true

tags: ["LeetCode", "DP", "树型DP"]
categories: ""
description: "递归求子问题：当前节点的二叉搜索子树的最大键值和。当前节点分为参与和不参与两种方案，都需要先求出子树的信息，在根据得到的信息进行汇总，得到当前二叉搜索子树的最大键值和。"
---

> [LeetCode 1373. 二叉搜索子树的最大键值和](https://leetcode.cn/problems/maximum-sum-bst-in-binary-tree/description/)
>
> [算法讲解079【必备】树型dp-下](https://www.bilibili.com/video/BV1ae411f7AC/)

### 思路

递归求子问题：当前节点的二叉搜索子树的最大键值和。当前节点分为参与和不参与两种方案，都需要先求出子树的信息，在根据得到的信息进行汇总，得到当前二叉搜索子树的最大键值和。

### 求解过程

**汇总信息**

需要先求出左树和右树的信息，再求当前树的信息，因此需要使用二叉树的后续遍历。返回的信息包括：

1. 树的最大值和最小值：用于判断是否是二叉搜索树。
2. 是否是二叉搜索树：用于判断是否是二叉搜索树。
3. 树的键值和：用于求最大键值和。
4. 二叉搜索子树的最大键值和：用于求最大键值和。

**递归过程**

1. 当前节点是空节点：最大值设置为整数最小值，最小值设置为整数最大值，设置为是二叉搜索树，键值和设置为 0，最大键值和设置为 0。
2. 当前节点是非空节点：先得到左右子树的信息，然后求出当前树的最大值和最小值，键值和，最大键值和，是否是二叉搜索树。

    a. 如果当前节点不参与：直接从左右子树的信息中求最大二叉搜索树的键值和。

    b. 如果当前节点参与：需要满足当前树是二叉搜索树的前提，然后将键值和与不参与时的键值和对比，取最大值。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(n)$

### 代码

```java
class Solution {
    public static class Info {
        boolean isBST;
        int maxValue;
        int minValue;
        int sum;
        int maxBSTSum;
        Info(boolean a, int b, int c, int d, int e) {
            isBST = a;
            maxValue = b;
            minValue = c;
            sum = d;
            maxBSTSum = e;
        }
    }
    public int maxSumBST(TreeNode root) {
        return process(root).maxBSTSum;
    }
    public Info process(TreeNode node) {
        if (node == null) {
            return new Info(true, Integer.MIN_VALUE, Integer.MAX_VALUE, 0, 0);
        }
        Info L = process(node.left);
        Info R = process(node.right);
        int ma = Math.max(node.val, Math.max(L.maxValue, R.maxValue));
        int mi = Math.min(node.val, Math.min(L.minValue, R.minValue));
        int sum = L.sum + R.sum + node.val;
        boolean isBST = L.isBST && R.isBST && node.val > L.maxValue && node.val < R.minValue;
        int maxBSTSum = Math.max(L.maxBSTSum, R.maxBSTSum);
        if (isBST) {
            maxBSTSum = Math.max(sum, maxBSTSum);
        }
        return new Info(isBST, ma, mi, sum, maxBSTSum);
    }
}
```