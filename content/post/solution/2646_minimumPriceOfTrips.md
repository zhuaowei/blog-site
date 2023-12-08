---
title: "LeetCode 2646. 最小化旅行的价格总和"
date: 2023-12-06T11:10:35+08:00
draft: false
math: true

tags: ["LeetCode", "图", "DFS", "DP", "每日一题"]
categories: ""
description: "2023-12-06 leetcode 每日一题，暴力递归 + 动态规划求解最少的旅行花费"
---

> [2646. 最小化旅行的价格总和](https://leetcode.cn/problems/minimize-the-total-price-of-the-trips/)

## 思路

一共有 n 个点，编号从 0 -> n - 1，一共有 n - 1 条单向边将这些点串起来。所以任意两点之间是有唯一通路的，不存在更短的路径。因此从 i 号点到达 j 号点，经过哪些点是确定的，也就是说整个旅行，每一个点经过的次数是确定的。

相邻的点不能同时打折，类似于 [198. 打家劫舍](https://leetcode.cn/problems/house-robber/) ，这道题也可以使用类似的方式解决，在遍历整个图的过程中，通过相邻节点的打折情况灵活选择是否打折，最终求得花费最小值。

## 求解过程

首先需要统计出所有旅行经过了哪些点，经过了几次。我这里使用暴力递归的方式，每次求出一个旅行的经过的点，最终求出所有旅行经过的点。

递归调用 `process` 函数，如果出发点等于目的地，出发地次数 +1，否则继续递归，如果从出发点能到达目的地，出发点次数 +1，返回 true，否则返回 false。

然后，根据统计好的点及次数，再次遍历整个图，求出最小值。调用 `getMinSum` 函数，需要传入 `discount`，它是一个 `boolean` 类型的值，表示父节点有没有打折，然后子节点就可以根据情况选择能够和是否打折。分别求出两种情况（如果有的话），取最大值，然后返回。

最后，因为递归存在重复调用的情况，我们使用一个 dp 表记录下递归调用的结果，如果结果已经存在，直接返回；如果结果不存在时再计算，计算完成后，将结果记录下来，再返回。

## 复杂度

- 时间复杂度：$O(nm)$

n 为节点的个数，m 为旅行的次数。求旅行经过哪些点时和计算最小花费时，时间复杂度为都是 n。

- 空间复杂度：$O(n)$

需要存储邻接表、节点次数表和dp表。

## 代码

```java
class Solution {
    public int minimumTotalPrice(int n, int[][] edges, int[] price, int[][] trips) {
        List<Integer>[] graph = new List[n];
        for (int i = 0; i < n; i++) {
            graph[i] = new ArrayList<Integer>();
        }
        for (int[] edge : edges) {
            graph[edge[0]].add(edge[1]);
            graph[edge[1]].add(edge[0]);
        }
        int[] cnt = new int[n];
        for (int[] trip : trips) {
            process(graph, cnt, trip[0], trip[1], -1);
        }
        // 使用备忘录优化递归时间。
        int[][] dp = new int[n][2];
        return getMinSum(graph, price, cnt, 0, -1, 0, dp);
    }
    // 从 index 出发，能够到达 target
    public boolean process(List<Integer>[] graph, int[] cnt, int index, int target, int fa) {
        if (index == target) {
            cnt[index]++;
            return true;
        }
        for (int node : graph[index]) {
            if (node != fa && process(graph, cnt, node, target, index)) {
                cnt[index]++;
                return true;
            }
        }
        return false; 
    }
    public int getMinSum(List<Integer>[] graph, int[] price, int[] cnt, int index, int fa, int discount, int[][] dp) {
        if (dp[index][discount] != 0) {
            return dp[index][discount];
        }
        int p1 = 0, p2 = 0, ans = 0;
        // 当前节点不打折
        for (int node : graph[index]) {
            if (node != fa) {
                p1 += getMinSum(graph, price, cnt, node, index, 0, dp);
            }
        }
        // 到达叶子节点，返回可以选择的最小值。
        if (p1 == 0) {
            dp[index][discount] = discount == 1 ? cnt[index] * price[index] : cnt[index] * (price[index] / 2);
            return dp[index][discount];
        }
        ans = p1 + cnt[index] * price[index];
        // 根据上个节点的打折情况，选择打折
        if (discount == 0) {
            for (int node : graph[index]) {
                if (node != fa) {
                    p2 += getMinSum(graph, price, cnt, node, index, 1, dp);
                }
            }
            ans = Math.min(ans, p2 + cnt[index] * (price[index] / 2));
        }
        dp[index][discount] = ans;
        return ans;
    }
}
```