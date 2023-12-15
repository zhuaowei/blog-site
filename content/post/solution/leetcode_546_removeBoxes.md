---
title: "LeetCode 546. 移除盒子"
date: 2023-12-15T22:02:43+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "区间DP"]
categories: ""
description: "定义子问题，在区间 [i, j] 上获得的最高分数，在区间 DP 时，附带一个参数 cnt，它表示在 i 之前有多少个与 i 相同的盒子与区间相连。"
---

> [LeetCode 546. 移除盒子](https://leetcode.cn/problems/remove-boxes/)
>
> [算法讲解077【必备】区间dp-下](https://www.bilibili.com/video/BV1du4y1L7gy/)

### 思路

根据题意，连续的相同盒子越多，分数就越高。所以在求区间最高分数的时候，要让相同的盒子尽量“连起来”。单纯的区间 DP 无法满足这种要求，需要定义一个额外的变量维护相同盒子的数量。

定义子问题，在区间 `[i, j]` 上获得的最高分数，在区间 DP 时，附带一个参数 cnt，它表示在 `i` 之前有多少个与 `i` 相同的盒子与区间相连。

### 求解过程

**Base Case**

1. `i > j`：区间不合法，返回 0。

**递归过程**

1. 根据贪心，我们要求最大分数，就要让相同的连续的盒子尽量多，所以继续从 `i` 往右搜索，看看与 `i` 相同的盒子数量最多可以扩充到多少。假设扩充到了 `start` 位置，`start` 位置的盒子与 `i` 位置不同，此时连续的与 `i` 相同的盒子数量为 `cnt`。
2. 前缀先消一：如果此时将所有的与 `i` 相同的盒子消掉，那么分数为 `cnt * cnt` 加上之后没有被消除的。
3. 前缀先消二：我让前面没有消掉的继续往右匹配，假设来到了 `m` 位置。`[start, m - 1]` 位置执行递归，`[m, j]` 区间执行递归，得到的一种消除方案的分数。
4. 同理，继续往右找，继续执行前缀先消二的步骤，得到一种消除方案的分数。在所有方案中取最大值。

### 复杂度

- 时间复杂度：$O(n^4)$
- 空间复杂度：$O(n^3)$

### 代码

```java
class Solution {
    public int removeBoxes(int[] boxes) {
        int n = boxes.length;
        int[][][] dp = new int[n][n][n]; // n^3
        return process(boxes, 0, boxes.length - 1, 0, dp);
    }
    public int process(int[] nums, int i, int j, int x, int[][][] dp) {
        if (i > j) {
            return 0;
        }
        if (dp[i][j][x] > 0) {
            return dp[i][j][x];
        }
        int start = i + 1;
        while (start <= j && nums[start] == nums[i]) {
            start++;
        }
        int cnt = start - i + x;
        // 情况一：前缀先消
        int res = cnt * cnt + process(nums, start, j, 0, dp);
        // 情况二：前缀分别跟随后续的数字一起消
        for (int k = start; k <= j; k++) {
            // 与 i 位置相同的盒子，且是连续一段的第一个位置
            if (nums[k] == nums[i] && nums[k] != nums[k - 1]) {
                res = Math.max(res, process(nums, start, k - 1, 0, dp) + process(nums, k, j, cnt, dp));
            }
        }
        dp[i][j][x] = res;
        return res;
    }
}
```