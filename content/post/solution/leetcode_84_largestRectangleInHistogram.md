---
title: "LeetCode 2332. 坐上公交的最晚时间"
date: 2024-09-18T22:00:35+08:00
draft: false
math: true

tags: ["LeetCode", "每日一题", "模拟"]
categories: ""
description: ""
---
> [LeetCode 2332. 坐上公交的最晚时间](https://leetcode.cn/problems/the-latest-time-to-catch-a-bus/)

### 思路

模拟乘客上车的顺序，如果最后还有公交有空位，则直接赶最后一辆公交的时间；如果没有空位，则找出最后一个上车的乘客的时间，逐个往前推。

### 求解过程

首先将公交和乘客重新排序，按照增序进行排列。使用双指针分别指向公交和乘客，模拟乘客上车。最终会有两种情况：

1、最后一辆公交还有位子可以坐（指公交还有空位，且最后一位乘客上车时间不是公交发车时间）：那么直接赶上公交发车时间 bus[n - 1];

2、最后一辆公交没有位子可以坐（指公交没有空位或者最后一位乘客上车时间恰好是公交发车时间）：那么需要从最后一位乘客上车时间逐分钟地往前遍历，选择插队时机。

为什么有位子还被分为第二种情况？其实是分为了需要插队和不需要插队两种。如果不需要插队，直接赶最后一班车的时间；如果需要插队，就需要从最后一位上车的乘客开始往前插队。

### 复杂度

排序时间复杂度为 $O(nlogn + mlogm)$；最多把公交和乘客都遍历一遍，时间复杂度为 $O(n + m)$，一共是 $O(nlogn + mlogm)$。

排序的空间复杂度为 $O(logn + logm)$，遍历空间复杂度为 $O(1)$，最终空间复杂度为 $O(logn + logm)$。

### 代码

```c++
class Solution {
public:
    int latestTimeCatchTheBus(vector<int>& buses, vector<int>& passengers, int capacity) {
        // 队列 双指针
        sort(buses.begin(), buses.end());
        sort(passengers.begin(), passengers.end());
        int n = buses.size();
        int m = passengers.size();
        int p1 = 0, p2 = 0;
        int space = 0;
        // 模拟
        for (int bus : buses) {
            space = capacity;
            while (space > 0 && p2 < m && passengers[p2] <= bus) {
                space--;
                p2++;
            }
        }
        // 从最后一个上车的人
        p2--;
        // 找到最后一个公交的时间或者乘客上车的时间
        int lastTime = space > 0 ? buses[n - 1] : passengers[p2];
        // 不断向前遍历，有空就插进去
        while(p2 >= 0 && passengers[p2] == lastTime) {
            p2--;
            lastTime--;
        }
        return lastTime;
    }
};
```
