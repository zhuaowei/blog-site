---
title: "LeetCode 235. 二叉搜索树的最近公共祖先"
date: 2024-02-25T22:43:51+08:00
draft: false
math: true

tags: ["LeetCode", "二叉树", "二叉搜索树"]
categories: ""
description: "2024-02-25 leetcode 每日一题。其中一种解法可以参考 leetcode 236 的解法，因为这题是二叉搜索树，因此还有另一种简单解法，就是利用最近公共祖先就是第一个在两个数字之间的节点。"
---

> [LeetCode 235. 二叉搜索树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-search-tree/)
> 
> [参考题解：数据结构和算法 - 二叉搜索树的最近公共祖先（3种解决方式）](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-search-tree/solutions/428739/er-cha-sou-suo-shu-de-zui-jin-gong-gong-zu-xian-3c)

### 思路

从二叉树根节点开始查找，判断该节点是否在两个节点之间，如果是，那么该节点就是最近的公共祖先，如果不是，那么就继续向下找。

### 求解过程

二叉搜索树的最重要的一个特性就是每一个节点，它大于所有的自己的左节点，小于所有的自己的右节点。如果某节点是两节点的公共祖先，那么该节点一定大于等于其中一个节点，且小于等于另一个节点（等于是因为可能就是二者之一）。

所以从根节点开始查找，如果发现当前节点处于二者之间，则说明该节点是最近公共祖先，否则，这两个节点应该位于当前节点的其中一侧，然后向这侧继续查找。

如何判断当前节点是否处于二者之间？如果是的，那么当前节点与二者之间的差值的符号应该不同，即 `root->val - p->val` 和 `root->val - q->val` 应该是异号的，或者其中之一为 `0`，那么二者的乘积应该是 `<= 0` 的，相反如果处于两侧，乘积应该是 `> 0` 的。

如果当前节点处于二者的其中一侧，那么 `root->val` 应该 大于二者或者小于二者（任意一个节点都是可以的）。如果 `root->val` 大于二者，那么应该向左侧继续查找，否则去右侧继续查找。

### 复杂度

- 时间复杂度：$O(log_2n)$
- 空间复杂度：$O(1)$

### 代码

```c++
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        while ((long)(root->val - p->val) * (root->val - q->val) > 0) {
            // 如果当前节点在 p q 的同一侧
            root = p->val < root->val ? root->left : root->right;
        }
        return root;
    }
};
```