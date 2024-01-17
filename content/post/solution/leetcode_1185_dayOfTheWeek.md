---
title: "LeetCode 1185. 一周中的第几天"
date: 2023-12-30T12:01:12+08:00
draft: false
math: true

tags: ["LeetCode", "数组", "暴力解法", "每日一题"]
categories: ""
description: "2023-12-30 leetcode 每日一题。计算出当前日期是距离 1970-01-01 的第几天，然后对天数取余，计算出当前日期是星期几。"
---

> [LeetCode 1185. 一周中的第几天](https://leetcode.cn/problems/day-of-the-week/)

### 思路

计算出当前日期是距离 1970-01-01 的第几天，然后对天数取余，计算出当前日期是星期几。

### 求解过程

主要是判断某一年是否是闰年，如何判断一个年份是否是闰年？**某一年如果能被 100 整除且能被400整除,或者不能被 100 整除但是能被 4 整除，那么这一年就是闰年。**

从 1970 年一直加到目标年份，然后根据是否闰年再进行月份天数计算，最终与 7 取余，计算出星期。

### 复杂度

- 时间复杂度：$O(n)$

n 为查询的平均年份

- 空间复杂度：$O(1)$

### 代码

```java
class Solution {
    String[] weeks = new String[] {
        "Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"
    };
    int[] days = new int[] {
        31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31
    };
    public String dayOfTheWeek(int day, int month, int year) {
        int sum = 0;
        for (int i = 1971; i < year; i++) {
            if (isLeapYear(i)) {
                sum += 366;
            } else {
                sum += 365;
            }
        }
        boolean leapYear = isLeapYear(year);
        for (int i = 1; i < month; i++) {
            sum += getDaysOfMonth(i, leapYear);
        }
        sum += day;
        return weeks[(sum + 4) % 7];
    }
    public boolean isLeapYear(int year) {
        return year % 100 == 0 ? year % 400 == 0 : year % 4 == 0;
    }
    public int getDaysOfMonth(int month, boolean leapYear) {
        if (leapYear && month == 2) {
            return 29;
        } else {
            return days[month - 1];
        }
    }
}
```