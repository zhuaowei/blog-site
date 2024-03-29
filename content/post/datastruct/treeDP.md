---
title: "树形DP"
date: 2022-12-01T19:11:18+08:00
draft: true
math: false

tags: ["数据结构", "算法", "树形DP"]
categories: ""
description: ""
---

树形DP是解决树的动态规划问题，包括二叉树和多叉树。

解决问题的核心就是分析树的头在参与或者不参与的情况下，可能得到的不同值，然后取得最大值。

> 例1：二叉树中有一个节点 a，从它出发到 b 节点的最大距离，其中每个节点只能经过一次。其实就是求二叉树中距离最大的值，每经过一个节点距离加一。

分析：

1. 以整棵树的头节点为例，如果最大距离有头节点参与，那么最大距离应该是 左树的高度 + 右树的高度 + 1。

2. 如果最大距离没有头节点参与，那么这两个节点应该在左树或者右树上，所以应该取 左树的最大距离和右树最大距离中最大的一个。

3. 将上面两种情况都考虑到，那么最大距离应该是 1 和 2 中最大的那个。

实现：

我们需要定义一个数据结构，来返回 DP 中需要的信息。根据上面的分析，我们应该返回 整个树的最大距离和树的最大高度。

最大距离是 1 和 2 中比较得来的；最大高度是 2 中取得，最后加一。

> 例2：有一家公司要聚会，一个老板低下有若干员工，一个员工低下有若干名下属，下属低下又有若干下属。每个人都有一个快乐值，当某位员工受邀后，其下属全部不能去，求如何发请柬才能使快乐值最大，返回最大的快乐值。

分析：对于这种 DP ，头参与不参与是一种分析的标准。

1. 如果头参与：那么其下属全部不能去，返回的结果是头的值 + 其每个下属在不能去的情况下的最大快乐值。

2. 如果不参与：其下属可以选择去或者不去，返回的结果是 0  + 其每个下属分别在去或者不去情况下的最大快乐值。

实现：根据上面的分析，我们也需要定义一个结构返回所需要的信息，即 头缺席时的最大快乐值和头到席时的最大快乐值。
