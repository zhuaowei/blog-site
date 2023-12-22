---
title: "LeetCode 1671. 得到山形数组的最少删除次数"
date: 2023-12-22T15:46:53+08:00
draft: false
math: true

tags: ["LeetCode", "最长递增子序列", "二分", "每日一题"]
categories: ""
description: "2023-12-22 leetcode 每日一题。求出每个位置左右两侧最长的递增子序列，取和最大的为结果，剩余的为最少的删除次数。"
---

> [LeetCode 1671. 得到山形数组的最少删除次数](https://leetcode.cn/problems/minimum-number-of-removals-to-make-mountain-array/)

### 思路

先正向求出每个位置最长的递增子序列长度，然后逆序求出每个位置最长的递增子序列的长度，同时求出最大的山脉宽度，剩下的就是最少的删除次数。

### 求解过程

定义一个 $end$ 数组，$end_i$ 表示 **长度为 $i + 1$ 的递增子序列的最小结尾**。

当来到 $i$ 位置时，在 $end$ 数组中找到一个大于等于 $nums_i$ 的最左侧的索引：

1. 如果存在为 $j$，则 $nums_i$ 可以替换掉 $end_j$ ，成为长度为 $j + 1$ 的递增子序列的最小结尾，$i$ 位置的最长递增子序列长度为 $j + 1$.
2. 如果不存在 $j$，则说明 $nums_i$ 可以添加到 $end$ 数组的后面，将最长递增子序列的长度扩充一个长度，$i$ 位置的最长递增子序列长度为 $len$。

我们使用一个数组 $pre$ 存储 $i$ 位置及以前的数组中最长递增子序列的长度。然后开始逆向遍历，求出 $i$ 位置及之后的数组中最长递增子序列的长度，二者相加再减去重复的 $i$ 位置，即为以 $i$ 位置为顶点的山地长度。求出了山地长度的最大值，也就得到了删除次数的最小值。

**注意**

因为山地的条件是：**山顶及左侧必须是递增子序列，山顶及右侧也必须是递增子序列**，所以在计算山脉长度之前，必须先判断 $i$ 位置左右两侧的最长递增子序列长度是否都大于等于 2。

### 复杂度

- 时间复杂度：$O(n \times log_2n)$
- 空间复杂度：$O(n)$

### 代码

```java
class Solution {
    public int minimumMountainRemovals(int[] nums) {
        // 最长递增子序列
        int n = nums.length;
        int[] end = new int[n];
        int[] pre = new int[n];
        end[0] = nums[0];
        pre[0] = 1;
        int len = 1;
        for (int i = 1, index; i < n; i++) {
            index = find(end, len, nums[i]);
            if (index == -1) {
                end[len++] = nums[i];
                pre[i] = len;
            } else {
                end[index] = nums[i];
                pre[i] = index + 1;
            }
        }
        int ans = 0;
        end[0] = nums[n - 1];
        len = 1;
        for (int i = n - 2, index, temp; i > 0; i--) {
            index = find(end, len, nums[i]);
            if (index == -1) {
                end[len++] = nums[i];
                temp = len;
            } else {
                end[index] = nums[i];
                temp = index + 1;
            }
            if (pre[i] > 1 && temp > 1) {
                ans = Math.max(ans, pre[i] + temp - 1);
            }
        }
        return n - ans;
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