---
title: "使用双端队列求滑动窗口的最大值"
date: 2022-11-18T21:35:38+08:00
draft: false
math: false

tags: ["数据结构", "算法", "双端队列", "滑动窗口最大值"]
categories: ""
description: ""
---

## 1、LeetCode 题目

[滑动窗口最大值](https://leetcode.cn/problems/sliding-window-maximum/)

给你一个整数数组 nums，有一个大小为 k 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 k 个数字。滑动窗口每次只向右移动一位。

返回 滑动窗口中的最大值 。

示例 1：

输入：nums = [1,3,-1,-3,5,3,6,7], k = 3

输出：[3,3,5,5,6,7]

解释：

```
滑动窗口的位置                  最大值
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

示例 2：

输入：nums = [1], k = 1

输出：[1]

## 2、双端队列

双端队列：可以从两端出队和入队的队列。Java 中有 DeQueue 类，也可以使用 LinkedList 类，都可以实现以下操作：

头部入队、头部出队

尾部入队、尾部出队

获取头部、获取尾部

本文使用的类是 LinkedList。

```java
头部入队: addFirst()
头部出队: removeFirst()
尾部入队: addLast()
尾部出队: removeLast()
获取头部: getFirst()
获取尾部: getLast()
```

## 3、窗口

窗口其实就是数组中的一个范围，它包括左边界 L和右边界 R，左边界不能超过右边界。窗口在变化的过程中，左边界和右边界只能向右移动，也可以不动，但是不能往左走。在初始情况下，窗口是不在数组中的，所以他们的值都是 `-1`。

## 4、详解原理

我们使用双端队列存储窗口中的最大值，存储的是数组对应的下标，因为有下标我们就可以获取数组的信息。

首先，我们将窗口变化的情况分为两种，一是 右边界右移，而是 左边界右移。

**1、右边界右移**

当右边界扩充到 i 位置时，arr[i] 相比于双端队列的最后一个元素 LAST 有以下三种情况：

    1.1、LAST > arr[i]，添加后不影响队列的单调性，直接添加。

    1.2、LAST == arr[i]，添加后队列不是绝对单调，所以需要将 LAST 出队，然后继续比对 LAST，循环往复，直到 `LAST > arr[i]` 或者队列为空，将 arr[i] 添加到队列尾部。

    1.3、LAST < arr[i]，和上面的 1.2 一样破坏了队列的绝对单调性，操作和上面的一样。

> 绝对单调性：指整个单调递增或单调递减，没有存在导数为 0 的情况。

**2、左边界右移**

当左边界扩充到 i 位置时（左边界不超过右边界），arr[i] 相比于双端队列第一个元素 FIRST 有两种情况（因为队列 FIRST 是窗口中最大的，不可能有 arr[i] 还大的）：

    2.1、arr[i] < FIRST，舍弃该元素并不影响窗口内的最大值，队列不坐任何操作。

    2.2、arr[i] == FIRST，如果 FIRST 的下标等于 i，说明 FIRST 就是 arr[i]，而 arr[i] 是要舍去的，所以 FIRST 从头部出队；如果 arr[i] == FIRST，但二者下标并不同，那么 FIRST 的下标必定大于 i（因为尾部入队保证绝对单调性），窗口范围不包括 i，也不影响窗口内的最大值，所以什么都不做。

**3、双端队列的实际意义**

双端队列中保持绝对单调性的实际含义，我们需要从尾部入队的情况来看：

    3.1、LAST > arr[i]，比尾部小，但是还是有可能成为窗口最大值的，只不过肯定比 LAST 晚。

    3.2、LAST == arr[i]，与尾部相等，但是下标比尾部大，所以 arr[i] 肯定比尾部晚过期。

    3.3、LAST < arr[i]，前面的 LAST 永远都没有机会称为窗口的最大值。

所以，双端队列的实际意义就是从前往后，依次有可能成为最大值的，且过期时间最晚的顺序。

## 5、Java 代码

这段代码最关键的有两段，就是 右边界右移 和 左边界右移 的代码。



**5.1、右边界右移代码**

```java
// 如果 LAST > nums[i]; 2.2 直接尾部入队
if (nums[list.getLast()] > nums[i]) {
    list.addLast(i);
} else {
    // 否则继续比对，直到队列为空
    while (!list.isEmpty()) {
        // 如果 LAST > nums[i]，可以不比了；否则出队继续比
        if (nums[list.getLast()] <= nums[i]) {
            list.removeLast();
        } else {
            break;
        }
    }
    // 最终入队
    list.addLast(i);
}
```

在右边界第一次右移时，队列是空的，所以加个判断条件，遇到空队列，直接添加到尾部。

```java
if (list.isEmpty()) {
    list.addLast(i);
    continue;
}
``

**5.2、左边界右移代码**

```java
// 如果 FIRST == nums[i - k]，并且下标相同才出队，否则什么都不做
if (nums[i - k] == nums[list.getFirst()] 
    && i - k == list.getFirst()) {
    
    list.removeFirst();
}
```

**5.3、完整Solution代码**

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        LinkedList<Integer> list = new LinkedList<>();
        int max = Integer.MIN_VALUE;
        int[] ans = new int[nums.length - k + 1];
        // 先把初始窗口建立起来
        for (int i = 0; i < k; i++) {
            if (list.isEmpty()) {
                list.addLast(i);
                continue;
            }
            if (nums[list.getLast()] > nums[i]) {
                list.addLast(i);
            } else {
                while (!list.isEmpty()) {
                    if (nums[list.getLast()] <= nums[i]) {
                        list.removeLast();
                    } else {
                        break;
                    }
                }
                list.addLast(i);
            }
        }
        ans[0] = nums[list.getFirst()];
        for (int i = k; i < nums.length; i++) {
            // 左边界右移
            if (nums[list.getLast()] > nums[i]) {
                list.addLast(i);
            } else {
                while (!list.isEmpty()) {
                    if (nums[list.getLast()] <= nums[i]) {
                        list.removeLast();
                    } else {
                        break;
                    }
                }
                list.addLast(i);
            }
            // 左边界右移
            if (nums[i - k] == nums[list.getFirst()] && i - k == list.getFirst()) {
                list.removeFirst();
            }
            // 右移一次，左移一次，然后记录窗口最大值
            ans[i - k + 1] = nums[list.getFirst()];;
        }
        return ans;
    }
}
```