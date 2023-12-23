---
title: "LeetCode 2828. 判别首字母缩略词"
date: 2023-12-20T22:04:39+08:00
draft: false
math: true

tags: ["LeetCode", "字符串", "数组", "每日一题"]
categories: ""
description: "2023-12-20 leetcode 每日一题。依次判断字符串数组中每个字符串的首字母，是否与目标字符串的字符相等。"
---

> [LeetCode 2828. 判别首字母缩略词](https://leetcode.cn/problems/check-if-a-string-is-an-acronym-of-words/)

### 思路

依次判断字符串数组中每个字符串的首字母，是否与目标字符串的字符相等。如果存在不相等的字符，返回 $false$；否则返回 $true$。

### 求解过程

在开始遍历之前，先判断字符串数组的长度与目标字符串的长度是否相同，如果不同，肯定无法对应，返回 $false$，否则开始遍历。

### 复杂度

- 时间复杂度：$O(n)$
- 空间复杂度：$O(1)$

### 代码

```java
class Solution {
    public boolean isAcronym(List<String> words, String s) {
        
        char[] str = s.toCharArray();
        int index = 0, n = str.length;
        if (words.size() != n) {
            return false;
        }
        for (String word : words) {
            if (word.charAt(0) != str[index]) {
                return false;
            }
            index++;
        }
        return true;
    }
}
```