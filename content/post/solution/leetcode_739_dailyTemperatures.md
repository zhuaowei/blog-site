---
title: "LeetCode 739. 每日温度"
date: 2024-09-18T00:16:34+08:00
draft: false
math: true

tags: ["LeetCode", "单调栈"]
categories: ""
description: ""
---
> [LeetCode 739. 每日温度](https://leetcode.cn/problems/daily-temperatures/)

### 思路

使用单调栈求出每个位置的下一个大于该值的索引。

### 求解过程

创建一个单调栈，和一个ans数组。单调栈维护一个递减的序列，每次来到 i 位置时，将当前值与栈顶值进行比较，如果当前值比较大，那么就把栈顶的值弹出，弹出的值右侧最近的比自己大的位置就是 i。一直弹出，直到栈为空，或者栈顶值比当前值大。弹出完毕后，将当前值入栈，继续下一个位置的判断。

### 复杂度

由于数组中每个位置的元素都最多有一次入栈机会和一次出栈机会，所以时间复杂度为 O(n)。

若数组中元素为降序，则栈的最大size为数组长度，空间复杂度为 O(n).

### 代码

```c++
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& temperatures) {
        int n = temperatures.size();
        vector<int> ans(n, 0);
        vector<int> st(n, 0);
        int r = 0;
        for (int i = 0; i < n; i++) {
            while (r > 0 && temperatures[i] > temperatures[st[r - 1]]) {
                r--;
                ans[st[r]] = i - st[r];
            }
            st[r++] = i;
        }
        return ans;
    }
};
```
