---
title: "LeetCode 2454. 下一个更大元素 IV"
date: 2023-12-12T11:20:50+08:00
draft: false
math: true

tags: ["LeetCode", "单调栈", "优先队列", "每日一题"]
categories: ""
description: "2023-12-12 leetcode 每日一题，使用单调栈和优先队列，求出数组中右侧第二个比自己大的数字。"
---

> [LeetCode 2454. 下一个更大元素 IV](https://leetcode.cn/problems/next-greater-element-iv/)
> 
> [苗 - 【CPP】单调栈 + 优先队列](https://leetcode.cn/problems/next-greater-element-iv/solutions/1935583/by-miao-z0-pnss/?envType=daily-question&envId=2023-12-12)

### 思路

将求解下下一个更大元素分成两步，先求下一个更大元素，如果有，存储起来，再求下下一个更大元素。

### 求解过程

使用一个栈和一个优先队列（也可以使用两个栈），一个用来计算出某元素是否有下一个更大元素，另一个存储 有下一个更大元素的集合。

遍历每一个元素，首先判断优先队列中是否有元素，如果有再看看当前元素是否大于它中的元素。因为**优先队列里存储的元素已经找到过一个更大元素了，如果当前元素再大于它，就找到两个了**。然后使用单调栈，计算出当前元素是否大于栈内的元素，如果大于，说明栈内的元素找到了一个更大元素。

**两个单调栈**

优先队列的目的是取出队列中最小的元素，依次判断当前元素是否大于它，如果为空或者不大于，就停止。

如果使用单调栈实现：判断是否有下一个更大值的栈，它是单调递减的，从这个栈中弹出，然后压入到另一个栈中，那么另一个栈中的元素就是单调递增的，所以要将栈栈弹出的元素翻转过来再进入另一个栈。

### 复杂度

- 时间复杂度：$O(n \times log_2n)$
- 空间复杂度：$O(n)$

### 代码

```java
class Solution {
    public int[] secondGreaterElement(int[] nums) {
        int n = nums.length;
        // 普通的单调栈，用于判断是否找到当前位置右侧第一个大于它的数字。
        int[] stack = new int[n];
        int r = 0;
        // 优先队列，加快查找，存储的是已经找到 右侧第一个大于它 的索引
        PriorityQueue<int[]> queue = new PriorityQueue<>((num1, num2) -> num1[1] - num2[1]);
        int[] ans = new int[n];
        Arrays.fill(ans, -1);
        for (int i = 0; i < n; i++) {
            while (!queue.isEmpty() && queue.peek()[1] < nums[i]) {
                // queue 里的数字是已经找到一个大于它的数字了，现在判断第二个是否大于它
                ans[queue.poll()[0]] = nums[i];
            }
            while (r > 0 && nums[stack[r - 1]] < nums[i]) {
                queue.add(new int[] {stack[r - 1], nums[stack[r - 1]]});
                r--;
            }
            stack[r++] = i;
        }
        return ans;
    }
}
```