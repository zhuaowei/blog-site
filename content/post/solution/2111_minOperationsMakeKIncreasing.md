---
title: "LeetCode 2111. 使数组 K 递增的最少操作次数"
date: 2023-12-09T10:46:28+08:00
draft: false
math: true

tags: ["LeetCode", "DP", "二分", "最长递增子序列"]
categories: ""
description: "将原数组分为 k 组，对每一组求最长非递减子序列的长度，剩余的造成递减的数字就是需要改的，计算个数。"

links:
  - title: 左程云的个人主页 - 哔哩哔哩视频
    description: 本人号，详解各种算法和数据结构，代码和资料：https://github.com/algorithmzuo
    website: https://space.bilibili.com/8888480
    image: 
---

> [2111. 使数组 K 递增的最少操作次数](https://leetcode.cn/problems/minimum-operations-to-make-the-array-k-increasing/)

## 思路

> 思路来自 [B站左程云的视频课程](https://www.bilibili.com/video/BV1ne411D7CQ/)，在文章末尾可以直接转到左程云的B站个人空间。

根据题意需要将原数组进行分组，一共分为 k 个组，求将每一组变成非降序子序列的最少操作数。那就求每一组的最长非降序子序列的长度，剩余的就是需要修改的数字个数。

## 求解过程

end 数组申请的空间为 `(n + k - 1) / k` 即可，每组数字的最大个数。

首先使用两层 for 循环，第一层为 k 个组，，第二层为遍历每一组的数字。

求出每一组的最长非降序子序列后，将每一组的最长长度加起来，使用原数组长度减去总和就是最少操作数。

> 详细的求解最长递增子序列以及最长非递减子序列：[LeetCode 300. 最长递增子序列](/article/leetcode-300.-最长递增子序列/)

## 复杂度

- 时间复杂度：$O(n \times log_2n)$

- 空间复杂度：$O(n)$

## 代码

```java
class Solution {
    public int kIncreasing(int[] arr, int k) {
        // 将 原数组进行分组，分成 k 组，
        // 分别求非降序子序列的最长长度，而那些降序的数字就是需要修改的。
        // 由于是非降序，不符合要求的数字修改为前一个或后一个非降序数字即可，操作次数 +1
        int n = arr.length, len;
        int[] end = new int[(n + k - 1) / k];
        int ans = 0;
        for (int i = 0; i < k; i++) {
            len = 1;
            // Arrays.fill(end, 0); // 不需要全置为 0，只在 [0, len) 范围搜索。
            end[0] = arr[i];
            for (int j = i + k, index; j < n; j += k) {
                index = find(end, len, arr[j]);
                if (index == -1) {
                    end[len++] = arr[j];
                } else {
                    end[index] = arr[j];
                }
            }
            // 结算
            ans += len;
        }
        return n - ans;
    }

    public int find(int[] end, int len, int target) {
        int left = 0, right = len - 1, mid, ans = -1;
        while (left <= right) {
            mid = left + (right - left) / 2;
            if (end[mid] > target) {
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
