---
title: "LeetCode 646. 最长数对链"
date: 2023-12-09T16:32:55+08:00
draft: false
math: true

tags: ["LeetCode", "排序", "DP", "二分", "最长递增子序列"]
categories: ""
description: "最长递增子序列扩展问题，先对数对进行排序，end 数组中存储 right 的最小值，查找时使用 left 进行查找"

links:
  - title: 左程云的个人主页 - 哔哩哔哩视频
    description: 本人号，详解各种算法和数据结构，代码和资料：https://github.com/algorithmzuo
    website: https://space.bilibili.com/8888480
    image: 
---

> [LeetCode 646. 最长数对链](https://leetcode.cn/problems/maximum-length-of-pair-chain/description/)

## 思路

> 思路来自 [B站左程云的视频课程](https://www.bilibili.com/video/BV1ne411D7CQ/)，在文章末尾可以直接转到左程云的B站个人空间。

使用最长递增子序列来求解。两个数对 $nums_i, nums_j (i < j)$ 要形成数对，需要 $right_i < left_j$，当来到 `j` 的时候，我们根据 $left_j$ 找到最长的递增数对即可。

## 求解过程

当来到 `j` 时，需要根据 $left_j$ 寻找，找到的数对它的 right 最大不能超过 $left_j$。我们使用 end 数组存储长度为 `i + 1` 的最长递增链的最小结尾。

每次我们找到 $\ge left_j$ 的最左侧位置。它有以下几种情况：

1. $right_i \ge left_j$ : 说明 $[left_j, right_j]$ 与 $[left_i, right_i]$ 有交集，无法直接添加到 i 后面，但是可以添加到 i - 1 后面，就是替代 i，i 和 j 结尾要取最小值，**更新 i 位置的为最小结尾**。
2. 没有找到 $\ge left_j$ 的位置: 也就是说 end 数组中存储的长度为 len 的数对结尾都小于 $left_j$ , j 位置的数对可以直接接到后面，使最长数对 +1。

## 复杂度

- 时间复杂度：$O(n \times log_2n)$

- 空间复杂度：$O(n)$

## 代码

```java
class Solution {
    public int findLongestChain(int[][] pairs) {
        // 按照开头进行排序
        Arrays.sort(pairs, (p1, p2) -> p1[0] - p2[0]);
        int n = pairs.length;
        // end 数组存储的是长度为 i + 1 的最长递增子序列的最小结尾 使用 right 作为结尾
        int[] end = new int[n];
        int len = 0;
        for (int i = 0, index; i < n; i++) {
            index = find(end, len, pairs[i][0]);
            if (index == -1) {
                end[len++] = pairs[i][1];
            } else {
                end[index] = Math.min(end[index], pairs[i][1]);
            }
        }
        return len;
    }

    public int find(int[] end, int len, int target) {
        int left = 0, right = len - 1, mid, ans = -1;
        while (left <= right) {
            mid = left + (right - left) / 2;
            if (end[mid] >= target) {
                ans = mid;
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return ans;
    }
}
```