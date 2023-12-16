---
title: "LeetCode 2962. 统计最大元素出现至少 K 次的子数组"
date: 2023-12-16T21:24:58+08:00
draft: false
math: true

tags: ["LeetCode", "滑动窗口"]
categories: ""
description: "最优解是用滑动窗口做，但是我没想到，用了单调栈，时间复杂度稍高，但是也过了。记录下灵神的滑动窗口解法。"
---

> [LeetCode 2962. 统计最大元素出现至少 K 次的子数组](https://leetcode.cn/problems/count-subarrays-where-max-element-appears-at-least-k-times/)
>
> 参考思路：[灵茶山艾府 - 滑动窗口（附题单！）Python/Java/C++/Go](https://leetcode.cn/problems/count-subarrays-where-max-element-appears-at-least-k-times/solutions/2560940/hua-dong-chuang-kou-fu-ti-dan-pythonjava-xvwg/)

### 思路

使用滑动窗口，如果窗口内的最大值数量为 k 个，则前面的以窗口左边界为结尾的子数组加上窗口就是满足题意要求的子数组。

### 求解过程

1. 先求出数组的最大值。
2. 窗口右边界从 0 出发，记录窗口内的最大值数量，如果遇到最大值，数量就加一。每次循环，右边界右扩一次。
3. 然后判断窗口内的最大值数量是否为 k，如果相等，左边界可以右移，一直移动到窗口内的最大值数量不等于 `k`。此时 区间 `[left - 1, right]` 的最大值数量是等于 `k` 的，那么前面每个以 `left` 结尾的子数组加上窗口，他们的最大值数量都是大于等于 `k` 的，他们的个数为 `left。`

### 复杂度

- 时间复杂度：$O(n)$
- 时间复杂度：$O(1)$

### 代码

```java
class Solution {
    public long countSubarrays(int[] nums, int k) {
        int n = nums.length;
        // 求最大值
        int target = Integer.MIN_VALUE;
        for (int num : nums) {
            if (num > target) {
                target = num;
            }
        }

        // 滑窗
        // 统计窗口内的最大值数量，如果满足条件，则区间前面的每一个数字与窗口组成的子数组都可以是满足要求的。
        int cnt = 0;
        long ans = 0;
        for (int l = 0, r = 0; r < n; r++) {
            if (nums[r] == target) {
                cnt++;
            }
            while (cnt == k) {
                cnt -= nums[l++] == target ? 1 : 0;
            }
            ans += l;
        }
        return ans;
    }
}
```