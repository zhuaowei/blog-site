---
title: "物理层——基本概念与常识"
date: 2022-10-29T22:01:32+08:00
draft: false
math: true

tags: ["计算机网络", "物理层"]
categories: ""
description: "介绍计算机网络物理层的基本概念"
---

## 1、物理层的基本概念

传输媒体接口的一些特性：

1.   **机械特性**：指明接口所用**接线器的形状、尺寸、引脚数目和排列、固定和锁定装置等**。
2.   **电气特性**：指明在接口电缆的各条线上出现的**电压范围**。
3.   **功能特性**：指明某条线上出现的某一电平的电压的意义。
4.   **过程特性**：指明对于不同功能的各种可能事件的出现顺序。

计算机内部是并行传输、在通信线路上是串行传输，所以物理层还要完成传输方式的转换。

## 2、数据通信基本常识

### 2.1、数据通信系统的模型

一个数据通信系统合一划分为三大部分：源系统（或者发送端、发送方）、传输系统（或传输网络）和目的系统（或接收端、接收方）。

-   **源点（source，源站、信源）**：源点设备产生要传输的数据，计算机产生的数字比特流。
-   **发送器**：通常源点产生的数字比特流需要通过发送器编码后才能够在传输系统中传输。典型的发送器就是调制器。
-   **接收器**：将传输系统传统过来的信号转换成能够被目的设备处理的信息。典型的接收器就是解调器。
-   **终点（destination，目的站、信宿）**：终点设备接受从接收器传输过来的数字比特流，然后把信息输出。

**消息（message）**：文字、语音、图像、视频等。通信的目的是传送消息。

**数据（data）**：是运送消息的实体。数据是使用特定方式标识的信息，通常是有意义的符号序列。

信号的分类：

1.   **模拟信号（连续信号）**：代表消息的参数的取值是连续的。
2.   **数字信号（离散信号）**：代表消息的参数的取值是离散的。

**码元**：在使用时间域（或者简称为时域）的波性标识数字信号时，代表不同离散数值的基本波性就成为码元。

二进制时只有两种码元：一种代表0，另一种代表1。

### 2.2、有关通信的基本概念

通信的基本方式：

1.   **单向通信（单工通信）**：只有一个方向的通信。
2.   **双向交替通信（半双工通信）**：同时只能有一个方向的通信，**不能同时**发送或者接收。
3.   **双向同时通信（双全工通信）**：双方可以同时发送和接受信息。

>   单工通信只使用一条信道，而半双工和双全工需要两条信道。双全工效率最高。

来自信源的基带信号通常包含较多低频成分，甚至有直流成分，而信道并不能传输这些，所以必须对基带信号进行调制。

**调制（Modulation）**：将数字信号调制成模拟信号。

**基带调制**：仅对基带信号的波性进行变换。

**带通调制**：使用**载波（carrier）**，把基带信号的频率范围搬移到较高的频段，并转换成模拟信号。

常用编码方式：

1.   **不归零制**：正电平代表1，负电平代表0。
2.   **归零制**：正脉冲代表1，负脉冲代表0。
3.   **曼彻斯特编码**：位周期中心向上跳变代表0，位周期中心向下跳变代表1。
4.   **差分曼彻斯特编码**：位开始边界有跳变代表1，位开始边界没有跳变代表0。

基本带通调制方法：

1.   **调幅（AM, Amplitude Modulation）**：载波的振幅随基带数字信号而变化。
2.   **调频（FM, Frequency Modulation）**：载波的频率随基带数字信号而变化。
3.   **调相（PM, Phase Modulate）**：载波的初识相位随基带数字信号而变化。

**正交振幅调制（QAM，Quadrature Amplitude Modulation）**：混合调制方法，提高传输速率。

### 2.3、信道的极限容量

虽然信号在信道中会失真，但是只要接收端能够识别出原来的信号，那么这种失真就对通信质量无影响。

**码元传输的速率越高，或者信号传输的距离越远，或者噪声干扰越大，或者传输媒体质量越差，在接收端的波性失真就越严重。**

1.   **奈氏准则**：在任何信道中，码元传输的速率是有上限的，传输速率超过此上限，就会出现严重的码间串扰的问题，使接收端对码元的判决成为不可能。
2.   **信噪比**：信号的平均功率和噪声的平均功率之比。记为 `S/N`，单位分贝（dB）。

$$
信噪比(dB) = 10 log_{10}{(S/N)} (dB)
$$

3.   **香农公式**：信道的带宽或信道中的信噪比越大，信息的极限传输速率就越高。

$$
C = W log_{2}{(1 + S/N)}
$$

>   C：信道的极限信息传输速率。

为了提高信息的传输速率，用编码的方法让**每一个码元携带更多比特的信息量。**

## 3、物理层下面的传输媒体

### 3.1、导引型传输媒体

1、**双绞线**

**定义**：也叫双纽线，最古老但又最常见的传输媒体。就是两根**互相绝缘，按规则绞合**的线。绞合可以减少相邻导线的电磁干扰。

为了提高抗干扰能力，在双绞线的外面再套一层金属丝编织成的屏蔽层，这就是**屏蔽双绞线（STP）**。

2、**同轴电缆**

**定义**：由内导体铜质芯线（单股实心线或多股绞合线）、绝缘层、网状编织的外导体屏蔽层一级保护塑料外层锁组成。由于外导体屏蔽层的作用，具有更好地**抗干扰特性**，广泛用于较高速率的传输。

3、**光缆**

**定义**：由非常透明的石英玻璃拉成细丝，主要由纤芯和包层构成双层通信圆柱体。

发射端使用发光二极管发出光脉冲，接收端用光电二极管做成检测器，还原电脉冲。

可以存在多条不同角度入射的光线在一条光纤中传输，这种就叫作**多模光纤**。如果只有细到只有一个光波传输，而不会产生反射，就叫作**单模光纤**。

**优点**：

1.   通信容量大。
2.   传输损耗小，适合远距离传输。
3.   抗雷电和电磁干扰性能好。
4.   无串音干扰，保密性好。
5.   体积小，重量轻。

### 3.2、非导引型传输媒体

无线传输可以使用的频段很广，根据频率可以将通信进行划分：

低频LF：（30kHz - 300kHz，波长：1km - 10 km）

中频MF：（300kHz - 3MHz）

高频HF：（30MHz - 30MHz）

甚高频（V，Very）：（30MHz - 300MHz）

特高频（U，Ultra）：（300MHz - 3GHz）

超高频（S，Super）：（3GHz - 30GHz）

极高频（E，Extremely）：（30GHz - 300GHz）
