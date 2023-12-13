---
title: "LeetCode 2697. 字典序最小回文串"
date: 2023-12-13T17:00:06+08:00
draft: false
math: true

tags: ["LeetCode", "数组", "双指针", "每日一题"]
categories: ""
description: "从两边向中间遍历，如果相等就跳过，如果不相等就让小的字符赋值为大的字符。"
---

> [LeetCode 2697. 字典序最小回文串](https://leetcode.cn/problems/lexicographically-smallest-palindrome/)

### 思路

从两边向中间遍历，如果相等就跳过，如果不相等就让小的字符赋值为大的字符。

### 求解过程

使用双指针分别指向字符串的开头和结尾，依次判断指针指向的字符是否相等：

- 相等：两指针向中心移动一格。
- 不相等：让较小的字符赋值给较大的字符的位置。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(n)$

### 代码

```java
class Solution {
    public String makeSmallestPalindrome(String s) {
        char[] str = s.toCharArray();
        int left = 0, right = str.length - 1;
        while (left < right) {
            if (str[left] < str[right]) {
                str[right] = str[left];
            } else if (str[left] > str[right]) {
                str[left] = str[right];
            }
            left++; right--;
        }
        return String.valueOf(str);
    }
}
```