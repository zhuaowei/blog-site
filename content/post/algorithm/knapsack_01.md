---
title: "01背包问题"
date: 2023-03-31T14:03:34+08:00
draft: true
math: false

tags: ["线性规划", "背包问题", "算法"]
categories: ""
description: ""
---

## 一、 题目描述

题目链接 acwing/leetcode


## 二、 思路

reference article: bilibili.com zhihu.com acwing.com

## 三、 c++代码

```
#include <iostream>
#include <cstring>
#include <algorithm>
using namespace std;
const int N = 1005;
int v[N];
int w[N];
int dp[N][N];
int main(void) {
    int n, V;
    cin >> n >> V;
    for (int i = 1; i <= n; i++) {
        cin >> v[i] >> w[i];
    }
    for (int i = 1; i <= n; i++) {
        int temp_v = v[i], temp_w = w[i];
        for (int j = 1; j <= V; j++) {
            if (j < temp_v) {
                dp[i][j] = dp[i - 1][j];
            } else {
                dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - temp_v] + temp_w);
            }
        }
    }
    cout << dp[n][V] << endl;
    return 0;
}
```
