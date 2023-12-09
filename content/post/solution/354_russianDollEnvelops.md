---
title: "LeetCode 354. 俄罗斯套娃信封问题"
date: 2023-12-08T22:00:14+08:00
draft: false
math: true

tags: ["LeetCode", "排序", "DP", "二分", "最长递增子序列"]
categories: ""
description: "通过排序将问题转化为最长递增子序列问题，然后求解。"

links:
  - title: 左程云的个人主页 - 哔哩哔哩视频
    description: 本人号，详解各种算法和数据结构，代码和资料：https://github.com/algorithmzuo
    website: https://space.bilibili.com/8888480
    image: 
---

> [354. 俄罗斯套娃信封问题](https://leetcode.cn/problems/russian-doll-envelopes/)

## 思路

> 思路来自 [B站左程云的视频课程](https://www.bilibili.com/video/BV1ne411D7CQ/)，在文章末尾可以直接转到左程云的B站个人空间。

信封不能翻转，首先想到排序，在宽度上较大的信封肯定能装下宽度较小的信封，在此基础上，应该尽量先装下高度较大的，所以在宽度上，从小到大排序。因为相同宽和高的信封并不能装下，只能装下宽和高比自己小的。所以在宽度相同时，需要将高度按从大到小排序。这样，同样宽度，高度小的就会自动略过前面比自己高的。

这样就转化成求排序后的高度最长递增子序列问题了。

## 求解过程

对信封进行排序，宽度从小到大。宽度相同时，高度从高到低。

此时，宽度相同时，因为前面高度较高，自动略过。前面宽度比 i 位置小时，找到 h[i] 能装下的最多信封个数，这些信封高度应该是递增的。也即求解高度的最长递增子序列。

## 复杂度

- 时间复杂度：$O(n \times log_2n)$

- 空间复杂度：$O(n)$

## 代码

```java
class Solution {
    public int maxEnvelopes(int[][] envelopes) {
        int n = envelopes.length;
        Arrays.sort(envelopes, 0, n, (en1, en2) -> {
            if (en1[0] == en2[0]) {
                return en2[1] - en1[1];
            } else {
                return en1[0] - en2[0];
            }
        });
        int[] end = new int[n];
        // 第 0 个位置肯定只能形成长度为 1 的递增子序列
        end[0] = envelopes[0][1];
        int len = 1;
        for (int i = 1, index, num; i < n; i++) {
            num = envelopes[i][1];
            index = find(end, len, num);
            if (index == -1) {
                end[len++] = num;
            } else {
                end[index] = num;
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