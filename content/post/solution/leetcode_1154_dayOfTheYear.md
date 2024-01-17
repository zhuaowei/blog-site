---
title: "LeetCode 1154. 一年中的第几天"
date: 2023-12-31T12:01:34+08:00
draft: false
math: true

tags: ["LeetCode", "数组", "暴力解法", "每日一题"]
categories: ""
description: "2023-12-31 leetcode 每日一题。这题跟昨天那道题很像，比昨天的还简单，同样也是根据年份是否闰年，计算出天数。"
---

> [LeetCode 1154. 一年中的第几天](https://leetcode.cn/problems/day-of-the-year/)

### 思路

这题跟昨天那道题很像，比昨天的还简单，同样也是根据年份是否闰年，计算出天数。

### 求解过程

按照月份逐月累加天数，2 月是一个特殊的月份，需要根据年份判断是否是闰年，如果是闰年，需要额外再加一天。

### 复杂度

- 时间复杂度：$O(1)$
- 空间复杂度：$O(1)$

### 代码

```java
class Solution {
    int[] daysOfMonth = new int[] {
        31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31
    };
    public int dayOfYear(String str) {
        char[] date = str.toCharArray();
        int year = 0, month = 0, day = 0;
        year = year * 10 + (date[0] - '0');
        year = year * 10 + (date[1] - '0');
        year = year * 10 + (date[2] - '0');
        year = year * 10 + (date[3] - '0');
        month = month * 10 + (date[5] - '0');
        month = month * 10 + (date[6] - '0');
        day = day * 10 + (date[8] - '0');
        day = day * 10 + (date[9] - '0');
        for (int i = 1; i < month; i++) {
            day += daysOfMonth[i - 1];
        }
        if (isLeapYear(year) && month > 2) {
            day += 1;
        }
        return day;
    }
    public boolean isLeapYear(int year) {
        return year % 100 == 0 ? year % 400 == 0 : year % 4 == 0;
    }
}
```