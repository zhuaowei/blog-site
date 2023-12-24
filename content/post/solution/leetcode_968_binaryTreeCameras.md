---
title: "LeetCode 968. 监控二叉树"
date: 2023-12-24T16:08:45+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "树型DP"]
categories: ""
description: "要使设置的摄像头数量尽量小，就需要让每个摄像头都尽量覆盖更多的节点，避免重复覆盖。因此可以使用贪心策略，自底向上，尽量使用最少的摄像头。"
---

> [LeetCode 968. 监控二叉树](https://leetcode.cn/problems/binary-tree-cameras/)
>
> [算法讲解079【必备】树型dp-下](https://www.bilibili.com/video/BV1ae411f7AC/)

### 思路

要使设置的摄像头数量尽量小，就需要让每个摄像头都尽量覆盖更多的节点，避免重复覆盖。因此可以使用贪心策略，自底向上，尽量使用最少的摄像头。

### 求解过程

**节点状态**

- 0：表示当前节点无摄像头监控，也无摄像头。
- 1：表示当前节点被摄像头监控，但是没有摄像头。
- 2：表示当前节点被摄像头监控，有摄像头。

**设置摄像头**

为了使摄像头个数最少，我们让叶子节点不设置摄像头，让其父节点设置摄像头。而一般节点则根据左右子节点的覆盖状态，来决定是否布置摄像头。

1. 当前节点为空节点：可以看作状态 1，被监控，但是没有摄像头，这样也不会影响上面的节点。
2. 当前节点为非空节点：

    a. 如果左右节点存在状态 0:说明子节点存在没有被覆盖的情况，这个时候就必须在当前节点设置摄像头，计数加一，返回状态 2。

    b. 否则继续判断，如果左右节点存在状态 2: 说明左右子节点不存在状态 0，存在状态 2，当前节点被子节点覆盖，不需要设置摄像头，返回状态 1。

    c. 否则，左右子节点只能都是状态 1：即左右子节点都被覆盖，但是没有摄像头，为了使摄像头数量最少，当前节点先不覆盖，让父节点覆盖，返回状态 0。

调用递归函数，判断头节点是否被覆盖。如果头节点返回状态 0，则头节点没有被覆盖，必须补上一个摄像头。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(n)$

### 代码

```java
class Solution {
    int ans = 0;
    public int minCameraCover(TreeNode root) {
        if (process(root) == 0) {
            ans++;
        }
        return ans;
    }
    // 定义状态
    // 1. 0 没有被监控，且没有摄像头
    // 2. 1 被监控，但是没有摄像头
    // 3. 2 被监控，且存在摄像头
    public int process(TreeNode node) {
        if (node == null) {
            return 1;
        }
        int status1 = process(node.left);
        int status2 = process(node.right);
        if (status1 == 0 || status2 == 0) {
            ans++;
            return 2;
        }
        if (status1 == 2 || status2 == 2) {
            return 1;
        }
        return 0;
    }
}
```