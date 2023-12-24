---
title: "LeetCode 437. 路径总和 III"
date: 2023-12-23T22:42:50+08:00
draft: false
math: true

tags: ["LeetCode", "前缀和", "DP", "树型DP"]
categories: ""
description: "记录下二叉树路径的前缀和，每来到一个节点就判断是否存在区间和为目标值。如果存在，就将记录加一。"
---

> [LeetCode 437. 路径总和 III](https://leetcode.cn/problems/path-sum-iii/)
> 
> [算法讲解079【必备】树型dp-下](https://www.bilibili.com/video/BV1ae411f7AC/)

### 思路

记录下二叉树路径的前缀和，每来到一个节点就判断是否存在区间和为目标值。如果存在，就将记录加一。

### 求解过程

因为求区间和只跟当前节点之前的路径有关，因此可以使用二叉树的前序遍历。

1. 遍历到某节点时，计算到当前位置的前缀和，从哈希表中查出要得出目标值需要的前缀和为多少，将次数加入到结果中。
2. 然后将当前位置的前缀和次数加一，记录到哈希表中，继续遍历左右子节点。遍历结束后，将当前位置的前缀和次数再减一。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(n)$

### 代码

```java
class Solution {
    HashMap<Long, Integer> count = new HashMap<>();
    int ans = 0;
    public int pathSum(TreeNode root, int targetSum) {
        count.put(0L, 1);
        process(root, targetSum, 0L);
        return ans;
    }
    public void process(TreeNode node, int target, long sum) {
        if (node == null) {
            return;
        }
        // 前缀和
        sum += node.val;
        ans += count.getOrDefault(sum - target, 0);
        count.put(sum, count.getOrDefault(sum, 0) + 1);
        process(node.left, target, sum);
        process(node.right, target, sum);
        count.put(sum, count.get(sum) - 1);
    }
}
```