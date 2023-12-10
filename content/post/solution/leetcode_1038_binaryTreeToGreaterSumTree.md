---
title: "Leetcode_1038_binaryTreeToGreaterSumTree"
date: 2023-12-05T12:04:36+08:00
draft: false
math: true

tags: ["LeetCode", "二叉树", "二叉搜索数", "Morris中序遍历", "每日一题"]
categories: ""
description: "使用 Morris 方式反向中序遍历二叉树，然后依次修改节点的值。"
---

> [LeetCode 1038. 从二叉搜索树到更大和树](https://leetcode.cn/problems/binary-search-tree-to-greater-sum-tree/)

### 思路

给定的是一个二叉搜索数，二叉搜索数具有以下几种特点：

- 节点的左子树仅包含键 小于 节点键的节点。
- 节点的右子树仅包含键 大于 节点键的节点。
- 左右子树也必须是二叉搜索树。

所以二叉搜索数的最右节点肯定是最大的，大于等于该值的节点只有自己。

对于任意一个头节点，它的右树的每一个节点肯定大于头节点，左树的每一个节点都小于头节点。所以我们可以按照 `右 -> 头 -> 左` 的顺序进行遍历，过程中记录下当前遇到的节点的和 sum，将节点值修改为 sum即可。

`右 -> 头 -> 左` 这种顺序恰好是中序遍历的逆序，所以将中序遍历修改一下就可以了。

### 求解过程

求解方式有递归法、迭代法和 Morris 法。这里只记录了 Morris 方法。

Morris 方法就是在遍历过程中，利用节点中空闲的指针，提前为自己的行程搭建捷径，然后“过河拆桥”，恢复为原来的状态。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(1)

### 代码
```java
class Solution {
    public TreeNode bstToGst(TreeNode root) {
        // 反向中序遍历
        int sum = 0;
        TreeNode node = root;
        TreeNode mostLeft = null;
        while (node != null)  {
            mostLeft = node.right;
            if (mostLeft != null) {
                while (mostLeft.left != null && mostLeft.left != node) {
                    mostLeft = mostLeft.left;
                }
                if (mostLeft.left == null) {
                    mostLeft.left = node;
                    node = node.right;
                    continue;
                } else {
                    mostLeft.left = null;
                }
            }
            sum += node.val;
            node.val = sum;
            node = node.left;
        }
        return root;
    }
}
```