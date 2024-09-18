---
title: "LeetCode 907. 子数组的最小值之和"
date: 2024-09-18T00:17:06+08:00
draft: false
math: true

tags: ["LeetCode", "单调栈"]
categories: ""
description: ""
---

> [LeetCode 907. 子数组的最小值之和](https://leetcode.cn/problems/sum-of-subarray-minimums/)

### 思路

以每个元素 i 为数组最小值的子数组，左侧和右侧最多能够延伸多长？求出每个元素的左侧和右侧第一个比自己小的元素位置。

### 求解过程

创建一个栈和两个与数组size等大的数组，分别存储当前元素 i 的左侧和右侧第一个比自己小的元素索引。如果左侧没有就填 -1，如果右侧没有就填 n （数组的大小）

使用单调栈维护一个递增的序列，如果当前位置 i 的元素比栈顶元素小，则出栈，出栈元素的右侧第一个比自己小的元素位置就确定了。（如果当前位置 i 的元素与栈顶元素相等，也出栈）。入栈时，如果栈顶有值，则当前位置 i 的元素左侧第一个比自己小的元素就找到了，否则就填 -1。

由于设置了默认值为 n，所以如果栈中还有元素，不必将每个元素再设置为 n。

最终结果为 sum(num[i] * (i - pre[i]) * (sub[i] - i) % mod)。根据模同余原理自己处理下取模。

> 疑问：为什么相等也出栈，最终答案不需要修正？

### 复杂度

遍历一遍数组，每个元素最多有入栈和出栈两种可能，时间复杂度为 O(n)。

使用三个与数组等长的数组作为栈和前后缀数组，空间复杂度为 O(n)。

### 代码

```c++
class Solution {
public:
    static const int MOD = 1e9 + 7;
    int sumSubarrayMins(vector<int>& arr) {
        // 求出每个数字左右两边第一个比自己小的数字的位置，与自身位置的差值相乘
        // (i - pre[i]) * (sub[i] - i)
        int n = arr.size();
        vector<int> st(n, 0);
        vector<int> pre(n, -1);
        vector<int> sub(n, n);
        int r = 0;
        for (int i = 0; i < n; i++) {
            while (r > 0 && arr[i] <= arr[st[r - 1]]) {
                r--;
                sub[st[r]] = i;
            }
            pre[i] = r > 0 ? st[r - 1] : -1;
            st[r++] = i;
        }
        long long ans = 0LL;
        for (int i = 0; i < n; i++) {
            ans = (ans + (long long)(i - pre[i]) * (sub[i] - i) % MOD * arr[i] % MOD) % MOD;
        }
        return ans;
    }
};
```
