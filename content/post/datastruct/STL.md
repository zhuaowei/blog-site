---
title: "C++中常用的STL"
date: 2023-03-06T15:35:51+08:00
draft: true
math: false

tags: ["C++", "算法竞赛", "STL"]
categories: ""
description: ""
---

一、排序与检索

二、vector

三、set

四、map

4.1、引入

```cpp
#include <map>
using namespace std; // 方便后面演示
```

4.2、声明

```cpp
map<string, int> myMap;
```

4.3、插入

```cpp
myMap.insert(pair<string, int> ("dict", 1));
mymap.insert(map<string, int>::value_type ("word", 2));
myMap["hello"] = 3;
```

4.4、遍历

```cpp
# 先声明一个迭代器，从 map 的开头开始，循环到 map 的结束
for (map<string, int>::iterator begin = myMap.begin(); begin != myMap.end(); begin++) {
    cout << begin->first << "," << begin->second << endl; // 输出要取地址
}

# 反向遍历，使用 reverse_iterator，相应的头尾需要改变。
for (map<string, int>::reverse_iterator rbegin = myMap.rbegin(); rbegin != myMap.rend(); rbegin++) {
    cout << rbegin->first << "," << rbegin->second << endl; // 输出要取地址
}
```

五、stack & queue

