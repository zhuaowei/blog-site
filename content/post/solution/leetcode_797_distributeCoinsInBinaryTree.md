---
title: "LeetCode 979. 在二叉树中分配硬币"
date: 2023-12-23T21:01:39+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "树型DP"]
categories: ""
description: "从树的底层依次向上求，求出当前节点平均分配硬币移动的最小步数。当前节点的最小步数为 树的节点个数减硬币个数的绝对值。"
---

> [LeetCode 979. 在二叉树中分配硬币](https://leetcode.cn/problems/distribute-coins-in-binary-tree/)
>
> [算法讲解079【必备】树型dp-下](https://www.bilibili.com/video/BV1ae411f7AC/)

### 思路

从树的底层依次向上求，求出当前节点平均分配硬币移动的最小步数。当前节点的最小步数为 树的节点个数减硬币个数的绝对值。

### 求解过程

当前节点的最小步数为 **树的节点个数减硬币个数的绝对值**。因此需要先使用后序遍历，求出左右子树的节点个数和硬币数。

1. 当前节点为空节点：节点个数为 0，硬币个数为 0。
2. 当前节点非空：先求出左右子树的节点个数和硬币个数，然后计算出当前树的节点个数和硬币树。求当前树的最小移动次数，累加到总答案中。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(n)$

### 代码

```java
class Solution {
    class Info {
        int coins;
        int count;
        Info (int a, int b) {
            coins = a;
            count = b;
        }
    }
    int ans = 0;
    public int distributeCoins(TreeNode root) {
        process(root);
        return ans;
    }
    public Info process(TreeNode node) {
        if (node == null) {
            return new Info(0, 0);
        }
        Info leftInfo = process(node.left);
        Info rightInfo = process(node.right);
        int count = leftInfo.count + rightInfo.count + 1;
        int coins = leftInfo.coins + rightInfo.coins + node.val;
        ans += Math.abs(count - coins);
        return new Info(coins, count);
    }
}
```