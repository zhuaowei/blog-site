---
title: "LeetCode 2415. 反转二叉树的奇数层"
date: 2023-12-15T16:24:52+08:00
draft: false
math: true

tags: ["LeetCode", "二叉树", "BFS", "每日一题"]
categories: ""
description: "2023-12-15 leetcode 每日一题，不用真的将节点对象进行翻转，而是将节点存储的值进行翻转。使用层序遍历，在遍历完奇数层之后，将节点中的值翻转。"
---

> [LeetCode 2415. 反转二叉树的奇数层](https://leetcode.cn/problems/reverse-odd-levels-of-binary-tree/)

### 思路

不用真的将节点对象进行翻转，而是将节点存储的值进行翻转。使用层序遍历，在遍历完奇数层之后，将节点中的值翻转。

### 求解过程

使用一个队列存储头节点，然后开始循环，直到队列为空。

在循环中，如果上一次遍历的是奇数层，则队列中存储的就是奇数层节点的值，执行翻转。这里使用一个 boolean 变量记录是否是奇数层。

然后遍历队列中的每一个节点，添加他们的左右子节点到队列中。（踩坑，不要用队列大小作为循环停止条件，要先计算出队列节点个数，确定循环次数）

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(n)$

### 代码

```java
class Solution {
    public TreeNode reverseOddLevels(TreeNode root) {
        if (root == null) {
            return root;
        }
        Queue<TreeNode> deque = new LinkedList<>();
        deque.add(root);
        TreeNode node;
        boolean reverse = false;
        while (!deque.isEmpty()) {
            int n = deque.size(), temp;
            if (reverse) {
                // 翻转
                List<TreeNode> list = new ArrayList<>(deque);
                TreeNode node1, node2;
                for (int j = 0; j < n / 2; j++) {
                    node1 = list.get(j);
                    node2 = list.get(n - j - 1);
                    temp = node1.val;
                    node1.val = node2.val;
                    node2.val = temp;
                }
            }
            reverse = !reverse;
            for (int i = 0; i < n; i++) {
                node = deque.poll();
                if (node.left != null) {
                    deque.add(node.left);
                }
                if (node.right != null) {
                    deque.add(node.right);
                }
            }
        }
        return root;
    }
}
```