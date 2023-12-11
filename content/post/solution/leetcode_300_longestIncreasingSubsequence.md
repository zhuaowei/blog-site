---
title: "LeetCode 300. 最长递增子序列"
date: 2023-12-08T15:01:44+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "二分", "最长递增子序列"]
categories: ""
description: "最长递增子序列问题，使用动态规划和二分，重点理解 end 数组的含义"

links:
  - title: 左程云的个人主页 - 哔哩哔哩视频
    description: 本人号，详解各种算法和数据结构，代码和资料：https://github.com/algorithmzuo
    website: https://space.bilibili.com/8888480
    image: 
---

> [300. 最长递增子序列](https://leetcode.cn/problems/longest-increasing-subsequence/)

## 思路

> 思路来自 [B站左程云的视频课程](https://www.bilibili.com/video/BV1ne411D7CQ/)，在文章末尾可以直接转到左程云的B站个人空间。

有两种方法，第一种是暴力解法，第二种是二分。

暴力解法：使用一个数组存储以 i 位置数字为结尾的最长升序子序列长度。每来到 i 位置，它的最长递增子序列至少是 1，就是自己，然后遍历 i 之前的所有结果，如果发现某位置 j 比 i 位置小，则递增子序列长度为 1 + dp[j]，在所有结果中求最大值。每次得到 dp[i] 后，比较记录最大值。

二分：使用一个 end 数组，end 数组存储的是**目前长度为 i + 1 的递增子序列的最小结尾**。

## 求解过程

这里只说二分解法。光从字面意思很难理解 end 数组的含义，举个例子：

假如当前来到了数组 `i` 位置，在 `end` 数组中寻找 `>= nums[i]` 的最左侧索引：

1. 如果找到了位置 `j`，则说明 `j` 以及之前所有数字可以和 `nums[i]` 形成以 `i` 位置为结尾的最长递增子序列。然后更新 `end[j]` 位置为 `nums[i]`.
2. 如果没有找到，说明 `nums[i]` 是 `end` 数组中最大的数，它可以把 `end` 数组往右扩，此时 `j` 为 `end` 数组的有效长度，`end[j] = nums[i]`，最长递增子序列为 `j + 1`。

`end` 数组总是保持递增的，上面两种情况的情况 1，`end[j] >= nums[i]`，使用 `nums[i]` 替换了 `end[j]`，`end[j]` 只会变得更小。而且后续 `end[j]` 变得再小也不会小于等于前一个数 `end[j - 1]`，因为如果后续 `nums[k] <= end[j - 1]`，那么它在 end 数组中找到的 `>= nums[k]` 的索引就一定不会是 `j`，而是小于 `j` 。

情况 2 找不到 `>= nums[i]` 的位置，所以直接在 `end` 数组右侧添加一个 `nums[i]`，所以 `end` 数组中总是保持递增的。

为什么找到了 `>= nums[i]` 在 `end` 数组中的位置就确定了 `[0, i]` 数组的最长递增子序列了呢？

通过上面的分析可以得知，`end` 数组中的所有值 `nums[j]` 都会在 `( nums[j - 1], nums[j] ]` 值之间，不会增大，只会减小。当 `nums[i]` 在 `end` 数组中找到位置后，那么它在数组中的最长递增子序列就确定了。因为在 `i` 之前比 `i` 小的数字已经在 end 数组中了，即使它后来可能被改变了，但是它会占一个位置，而这个位置是不会消失的。

回过头来再看 `end` 数组的定义，`end` 数组中每个数字都只会变小，变小后的值就是**当前数组的长度为 j + 1 的递增子序列的最小结尾**。

> `end` : 目前长度为 `i + 1` 的递增子序列的最小结尾

知道了 `end` 数组是递增的，就可以使用二分法求出 `>= nums[i]` 的索引。

## 复杂度

- 时间复杂度：$O(n \times log_2n)$

- 空间复杂度：$O(n)$

## 扩展

如果是非降序序列，很简单，在寻找 `>= nums[i]` 时修改为 `> nums[i]` 即可。这样 `end` 数组中就可以存储非降序的序列，结果也就是非降序的了。

## 代码

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        int n = nums.length;
        int[] end = new int[n];
        // 第 0 个位置肯定只能形成长度为 1 的递增子序列
        end[0] = nums[0]; 
        int len = 1;
        for (int i = 1; i < n; i++) {
            int index = find(end, len, nums[i]);
            if (index == -1) {
                end[len++] = nums[i];
            } else {
                end[index] = nums[i];
            }
        }
        return len;
    }
    // 找到 >= target 的最左侧索引
    public int find(int[] end, int len, int target) {
        int left = 0, right = len - 1, mid;
        int ans = -1;
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