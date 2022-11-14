---
title: "算法复杂度介绍、三种常见排序算法以及二分查找算法"
date: 2022-11-14T15:45:35+08:00
draft: false
math: true

tags: ["数据结构", "算法", "排序", "二分查找"]
categories: ""
description: ""
---

## 1、时间复杂度

### 1.1、操作单元

程序执行的一个最基本的操作。例如获取数组中第 i 个元素的值，只需计算它的偏移量即可；跳转一次链表，指向下一个单元；执行一次交换操作等。

### 1.2、时间复杂度

**估算**的执行程序所需的**执行操作单元的次数**，以选择排序为例，计算选择排序算法的时间复杂度。

选择排序是遍历一边数组，找出数组中数值最小的一项，与第一项交换，然后从第二项开始再次找到最小的元素，与第二项交换，以此类推，直到最后。

那么算法需要执行

1.   遍历操作数为 `1 + 2 + 3 + ... + n` 次，一共是 `[n(n+1)]/2` 次。

2.   比较次数与上面一样 `[n(n+1)/2]` 次

3.   交换操作 n 次。

     一共是 $n^2 + 2n$ 次，保留最高项，去掉最高项的系数，所以选择排序的事件复杂度为 $O(n^2)$ 。

     >   当数据量很大的时候，舍去的项就显得非常微小了，所以这也是只保留最高项的原因。
     >
     >   当两个时间复杂度相同的算法比较好坏时，需要分析不同样本下程序运行所需要的事件。

## 2、排序算法

### 2.1、选择排序

第 i 次循环，找出index >= i 中值最小的项，与第 i 项交换（i = 0, 1, 2, ..., N）

>   C/C++

```c++
// 交换两个元素
int* swap(int arr[], int i, int j){
    int temp = arr[i];
    arr[i] = arr[j];
    arr[j] = temp;
}

int* selectionSort(int arr[], int length){
    // 排除空数组和长度为1 的数组
    if (arr == nullptr || length < 2) {
        return arr;
    }
	// 循环开始
    for (int i = 0; i < length; i++) {
        // 最小值的 index
        int minIndex = i;
        for (int j = i; j < length; j++) {
            // 比较一下，更新最小值的索引
            if (arr[minIndex] > arr[j]) {
                minIndex = j;
            }
        }
        swap(arr, minIndex, i);
    }
    return arr;
}
```

### 2.2、冒泡排序

第一次循环，依次比较第 1，2个；2，3个；3，4个；...；N - 1个，N个元素的大小，不符合排序顺序的就交换。

第二次循环，依次比较第 1，2个；2，3个；3，4个；...；N - 2个，N - 1个元素的大小，不符合排序顺序的就交换。

第二次循环，依次比较第 1，2个；2，3个；3，4个；...；N - 3个，N - 2个元素的大小，不符合排序顺序的就交换。

...

>   C/C++

```c++
int* swap(int arr[], int i, int j){
    int temp = arr[i];
    arr[i] = arr[j];
    arr[j] = temp;
}

int* bubbleSort(int arr[], int length) {
    for (int i = 0; i < length - 1; i++) {
        for (int j = 0; j < length - 1 - i; j++) {
            if (arr[j] > arr[j + 1]) {
                swap(arr, j, j + 1);
            }
        }
    }

    return arr;
}
```

### 2.3、插入排序

从左到右逐步实现有序，就像打扑克整理牌一样。

第一步，实现 0 - 0 有序，必然是有序的，可以跳过

第二步，实现 0 - 1 有序，如果 arr1 < arr0，则交换。

第三步，实现 0 - 2 有序，如果arr2 < arr1，交换；再继续向前比较，如果还是arr2（这个时候已经变成arr1了）小，继续交换，以此类推...

...

直到第N步，实现整个数组的排序。

```java
public static void swap(int[] arr, int i, int j) {
    int temp = arr[i];
    arr[i] = arr[j];
    arr[j] = temp;
}

public static void insertSort(int[] arr) {
    for (int i = 1; i < arr.length; i++) {
        for (int j = i - 1; j >= 0 && arr[j] > arr[j + 1]; j--) {
            swap(arr, j, j + 1);
        }
    }
}
```

### 2.4、插入排序的时间复杂度

如果考虑最好的情况，就是排序好的数组，每次循环只需看一下，不满足循环条件就到下一次循环。这种情况的时间复杂度是O(N)。

如果是最坏的情况，与预期排序的顺序完全相反，那么第 i 次循环救药执行 i 次，就是1 + 2 + 3 + ... + N，这个时间复杂度就是 $O(N^2)$ 。

算法时间复杂度只考虑最坏的情况。

## 3、交换

### 3.1、普通交换

使用一个中间变量暂时存储其中一个变量，这个变量把另一个变量存储好后，就把临时变量赋值给另一个变量。

```c++
int* swap(int arr[], int i, int j){
    int temp = arr[i];
    arr[i] = arr[j];
    arr[j] = temp;
}
```

### 3.2、加法交换

变量1存储两个数的和，然后把变量1减变量2的结果赋值给变量2，最后把变量1减去变量2的结果赋值给变量1。

```c++
int* swap(int arr[], int i, int j){
    arr[i] = arr[i] + arr[j];
    arr[j] = arr[i] - arr[j];
    arr[i] = arr[i] - arr[j];
}
```

不需要借助临时变量。

### 3.3、异或交换

**1、异或**

异或用符号 `^` 表示，同位的数字相同为0，不同为1。

```
a   111010
b   010111
^   101101
```

**2、异或特性**

1.   任何值异或0都是本身
2.   任何值与自己异或都是0
3.   满足交换律和结合律

**3、交换**

```
int* swap(int arr[], int i, int j){
    arr[i] = arr[i] ^ arr[j];
    arr[j] = arr[i] ^ arr[j];
    arr[i] = arr[i] ^ arr[j];
}
```

原理：

第一步 `arr[i] = arr[i] ^ arr[j];`

第二步 `arr[j] = arr[i] ^ arr[j] = arr[i] ^ arr[j] ^ arr[j] = arr[i] ^ 0 = arr[i];`

第三步 `arr[i] = arr[i] ^ arr[j] = arr[i] ^ arr[j] ^ arr[i] = arr[j] ^ 0 = arr[j];`

注意：**这种方法必须保证交换的双方指向的不是同一块内存地址。不推荐使用**

位操作的速度比一般加减乘除快。

**4、相关题目**

1.   给一个数组，里面包含一个出现奇数次的数和其他出现偶数次的数，请你找出这个出现奇数次的数。

解法：定义一个变量 `eor = 0;` 然后与数组中的每一个数都异或，得到的结果就是出现奇数次的数。

因为**出现偶数次的数**以及**出现奇数次中的偶数部分**异或之后都为 0，在异或一次出现奇数次的数就得到结果了。

```c++
int oneOdd(int arr[], int length) {
    // 初始值为 0
    int eor = 0;
    for (int i = 0; i < length; i++) {
        eor ^= arr[i];
    }

    return eor;
}
```

2.   给一个数组，里面包含两个出现奇数次的数和其他出现偶数次的数，请你找出这两个出现奇数次的数。

解法：首先还是定义一个 `eor = 0` ，先异或一遍数组，得到的结果是 `eor = a ^ b;` （假设这两个数是a和b，a != b），

那么这两个数肯定有某个位置是不相同的，而这些位置在eor中就是1所在的位置。我们根据其中一个不相同的位置将数据划分为两个部分，定义一个变量 `eor2 = 0` 异或一遍其中一部分，得到的就是其中的 a 或 b，最后再把 `eor ^ eor2` 得到另一个数。

```c++
int* twoOdd(int arr[], int length) {
    int eor = 0;
    for (int i = 0; i < length; i++) {
        eor ^= arr[i];
    }

    int eor2 = 0;
    // 重要的一步，得到eor最右边为1的数。
    int rightOne = eor & (~eor + 1);
    for (int i = 0; i < length; i++) {
        // 将每一个数都与 rightOne 按位与
        // 如果那个位置是 0，则结果为 0，写 != 也是一样，目的是分开数据。
        if ((arr[i] & rightOne) == 0) {
            eor2 ^= arr[i];
        }
    }
    printf("%d, %d\n", eor2, (eor ^ eor2));
}
```

假设第一次循环得到的eor是 `1 0 0 1 0 0` ，`int rightOne = eor & (~eor + 1);` 这一步做的就是：

1.   eor取反 `0 1 1 0 1 1`
2.   加一 `0 1 1 1 0 0`
3.   再和最初的eor相与 `0 0 0 1 0 0` ，这样就得到了 a 和 b 最右边不相同位的数字。

4.   之后数组中每个数字都与这个数字相与，那么倒数第三位为 1 的就不为0，为0的就等于0，这样就把两种数字区分开了。

## 4、二分查找

### 4.1、有序数组二分查找

对于有序数组，从从查找一个数字的位置可以使用二分查找的方法。

第一步，设置left和right以及middle，middle就是left + right / 2，将要查找的值与第 middle 个值进行比较，如果要查的值比较小，就更新right，如果要查的值计较大，就更新left。最后再更新middle。

然后一直重复上面的步骤，直到找到那个值的位置，或者left > right。

```java
public static int binarySearch(int[] arr, int value) {
    int left = 0;
    int right = arr.length - 1;
    while (left <= right) {
        int middle = (left + right) / 2;
        if (arr[middle] > value) {
            right = middle - 1;
        } else if (arr[middle] < value) {
            left = middle + 1;
        } else {
            return middle;
        }
    }
    return -1;
}
```

### 4.2、二分查找的时间复杂度

二分查找的最复杂的情况就是查找到left > right，也就是 log(N)。因为每循环一次就去掉现有区域的一半。例如 8 4 2 1。

### 4.3、扩展

1.   给一个有序数组，查找大于x的最左侧的值的索引。

使用二分查找的思路就是，先用一个临时变量存储最接近目标值的索引，然后继续查找有没有更接近的，如果middle所指的值比指定的值要大，那么就更新临时变量，如果比指定值小（或相等），就不变。最后更新left，right，middle，直到left > right。

与一般二分查找不同的是，这个二分查找一定要搜索到底。

```java
public static int leftBinarySearch(int[] arr, int value) {

    int left = 0;
    int right = arr.length - 1;
    int res = right;

    while (left <= right) {
        int middle = left + (right - left) / 2;
        if (arr[middle] > value) {
            res = middle;
            right = middle - 1;
        } else {
            left = middle + 1;
        }
    }

    return res;
}
```

2.   找出（无序）数组中一个局部最小值。

如果第一个数字比第二个小，第一个值就是局部最小值。同理最后一个比倒数第二个小，也是局部最小值。

第 i 个值小于第 i - 1 个，也小于 第 i + 1 个，他也是局部最小值。

假如满足arr[0] < arr[1] 就返回零，否则查看 arr[N - 1] < arr[N - 2]，如果满足，返回 N - 1，否则进入下一步。

如果上面两个都不满足，那么这个数组两端就是翘的，`\ ..... /` ，其中肯定有局部最小值点。

如果是 `\.../m/.../` 或者 `\.../m\.../` ，如果左侧较小，那么左边肯定有最小值点如果右侧较小，右边肯定有最小值点，如果两个条件都没有满足，那么middle就是最小值点。

```java
// TODO
```