---
title: "LeetCode 162. 寻找峰值"
date: 2023-12-18T20:06:54+08:00
draft: false
math: true

tags: ["LeetCode", "二分", "每日一题"]
categories: ""
description: "2023-12-18 leetcode 每日一题，使用二分法，根据二分范围两端和中点的上升或下降趋势，来判断那个部分是存在极值的，然后向那个方向继续二分。"
---

> [LeetCode 162. 寻找峰值](https://leetcode.cn/problems/find-peak-element/)

### 思路

先判断左右两端的上升或者下降趋势，然后根据区间中点左右两侧的趋势判断哪部分存在极值，然后继续二分。如果中点比左右两侧都大，说明中点就是极值。

### 求解过程

1. **特判**：因为题目说数组范围外的数字视为负无穷，如果数组开头是下降趋势，则数组开头就是极值；同理，如果数字末尾是上升趋势，则数组末尾是极值。如果数字长度为 1，那么 0 就是极值位置。

2. **二分**：如果数字开头和结尾都不是极值，则说明整个数字是 `/ .... \` 这样的，那么在其中肯定存在一个极值。

    a. 如果中点比左边的小：`/ ...\m ...\`，则左边肯定存在极值。

    b. 如果中点比右边的小：`/ ... /m/ ... \`，则右边肯定存在极值。

    c. 如果中点比左右两侧都大：`/ ... /m\ ... \`，则中点为极值点。

### 复杂度

- 时间复杂度：$O(log_2n)$
- 空间复杂度：$O(1)$

### 代码

```java
class Solution {
    public int findPeakElement(int[] nums) {
        // 二分
        int n = nums.length;

        if (n == 1) {
            return 0;
        }
        if (nums[0] > nums[1]) {
            return 0;
        }
        if (nums[n - 2] < nums[n - 1]) {
            return n - 1;
        }
        int left = 1, right = n - 2, ans = 0, mid;
        while (left <= right) {
            mid = left + (right - left) / 2;
            if (nums[mid - 1] > nums[mid]) {
                right = mid - 1;
            } else if (nums[mid] < nums[mid + 1]) {
                left = mid + 1;
            } else {
                ans = mid;
                break;
            }
        }
        return ans;
    }
}
```