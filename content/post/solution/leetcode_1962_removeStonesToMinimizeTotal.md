---
title: "LeetCode 1962. 移除石子使总数最小"
date: 2023-12-23T11:46:09+08:00
draft: false
math: true

tags: ["LeetCode", "堆排序", "每日一题"]
categories: ""
description: "2023-12-23 leetcode 每日一题。每次找出数组中的最大值，使其值减半，然后循环 k 次，求出最终的数组之和。"
---

> [LeetCode 1962. 移除石子使总数最小](https://leetcode.cn/problems/remove-stones-to-minimize-the-total/)

### 思路

每次找出数组中的最大值，使其值减半，然后循环 k 次，求出最终的数组之和。

### 求解过程

因为数组是会变化的，需要时刻维护数组中的最大值，因此可以使用堆来排序，使用大根堆每次取出数组中的最大值。

1. 首先将数组中的石头数量添加到堆。
2. 使堆顶的数字减半，这个时候并不需要出堆，只需要将减半后的堆顶向下压。
3. 循环步骤 2，执行 k 次后，求出最终数组的和。

### 复杂度

- 时间复杂度：$O(n \times log_2n)$
- 空间复杂度：$O(n)$

### 代码

```java
class Solution {
    int[] heap;
    int size = 0;
    public int minStoneSum(int[] piles, int k) {
        // 大根堆
        int n = piles.length;
        heap = new int[n];
        for (int i = 0; i < n; i++) {
            add(piles[i]);
        }
        for (int i = 0; i < k; i++) {
            poll();
        }
        int sum = 0;
        for (int i = 0; i < n; i++) {
            sum += heap[i];
        }
        return sum;
    }
    public void add(int value) {
        heap[size++] = value;
        int index = size - 1;
        int up = (index - 1) / 2;
        while (heap[index] > heap[up]) {
            swap(heap, index, up);
            index = up;
            up = (index - 1) / 2;
        }
    }
    public int poll() {
        int value = heap[0];
        heap[0] -= heap[0] / 2;
        // 将 0 位置向下压
        int index = 0;
        int left = index * 2 + 1;
        while (left < size) {
            int larger = heap[index] > heap[left] ? index : left;
            int largest = left + 1 < size && heap[left + 1] > heap[larger] ? left + 1 : larger;
            if (largest == index) {
                break;
            } else {
                swap(heap, index, largest);
            }
            index = largest;
            left = index * 2 + 1;
        }
        return value;
    }
    public void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```