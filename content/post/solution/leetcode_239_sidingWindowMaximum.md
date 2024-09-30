---
title: "LeetCode 239. 滑动窗口最大值"
date: 2024-09-28T23:22:52+08:00
draft: false
math: true

tags: ["LeetCode", "单调队列", "滑动窗口"]
categories: ""
description: ""
---
> [LeetCode 239. 滑动窗口最大值](https://leetcode.cn/problems/sliding-window-maximum/description/)

### 思路

模板题，使用单调队列求解。

### 求解过程

创建一个队列，和指向队列首尾的指针。队列中维护递减的序列，右边界右扩时添加到队列，左边界缩小时检测一下是否为队首，如果是，放弃队首。

详细过程看代码。

### 复杂度

每个元素都是最多入队一次，出队一次，时间复杂度为 $O(n)$。
需要一个数组等长的队列，空间复杂度为 $O(n)$

### 代码

```c++
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        int n = nums.size();
        // 队列，存储窗口内的最大值
        vector<int> st(n, 0);
        // 指针，指向队列的首尾
        int h = 0, r = 0;
        vector<int> ans;
        for (int i = 0 ; i < n; i++) {
            // 维护一个递减的队列，如果队列中有元素，且当前元素比队尾更大，弹出队尾
            while (r > h && nums[i] >= nums[st[r - 1]]) {
                r--;
            }
            st[r++] = i;
            if (i >= k - 1) {
                // 维护窗口，如果窗口左边界是队首，放弃队首
                if (i - k == st[h]) {
                    h++;
                }
                // 处理之后的队列，队首就是窗口最大值。
                ans.push_back(nums[st[h]]);
            }
        }
        return ans;
    }
};
```
