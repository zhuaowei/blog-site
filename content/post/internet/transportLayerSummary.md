---
title: "计算机网络运输层概述"
date: 2022-10-30T19:52:27+08:00
draft: false
math: true

tags: ["计算机网络", "运输层"]
categories: ""
description: ""
---

## 1、运输层概述

### 1.1、进程之间通信

**运输层向它上面的应用层提供通信服务，它数据面向通信部分的最高层，同时也是用户功能的最底层。**

端到端的通信不是指主机与主机之间的通信，准确来说，是**主机进程之间的通信**，而通信的真正端点就是主机中的进程。

在一台主机中，经常有多个应用进程和其他主机的多个进程之间进行通信，所以运输层需要一个很重要的功能，就是**复用（multiplexing）和分用（demultiplexing）**。

运输层提供应用进程间的逻辑通信。**逻辑通信**的意思是好像通信是沿着水平方向进程的，实际上是依赖底层的协议实现的，他们之间并没有直接的物理连接。

运输层需要对收到的报文进行差错检测，而 IP 数据报只检验首部。

根据不同的需求，运输层提供了两种运输协议，即**面向连接的TCP**和**无连接的UDP**。

另外，**运输层向高层屏蔽了下面的网络核心的细节他是应用进程看见的就是好像再两个运输层实体之间有一条端到端的逻辑通信信道**。

### 1.2、运输层的两个主要协议

1.   **用户数据报协议UDP（User Datagram Protocol）**
2.   **传输控制协议TCP（Transmission Control Protocol）**

它们对应的**运输协议数据单元TPDU（Transport Protocol Data Unit）**为**UDP用户数据报**和**TCP报文段（segment）**。

### 1.3、运输层的端口

**复用**：应用层所有的应用进程都可以通过运输层再传送到IP层。

**分用**：运输层从IP层收到发送给各应用进程的数据后，必须分别交付到指明的各应用进程。

**端口（port）**：协议端口号（protocol port number），是用来标志进程的，是**应用层的各种协议进程与运输实体进程层间交互的一种地址**。

TCP/IP 使用 **16 位端口号**来标志一个端口，端口号**只具有本地意义**。范围 `0 - 65535`。

**端口号的划分**：

1.   **服务器端使用的端口号**：
     1.   **熟知端口号（well-known port number）/系统端口号**：`0 - 1023`，一些比较重要的系统服务进程。
     2.   **等级端口号**：`1024 - 49151`，也即 $[2^{10}, \frac{3}{4}\times2^{16} - 1]$
2.   **客户端使用的端口号**：`49152 - 65535`，也即 $[\frac{3}{4}\times2^{16}, 2^{16} - 1]$，只有再客户进程运行时才动态选择，所以又叫短暂端口号。

熟知端口号：

| 应用进程     | 熟知端口号 |
| ------------ | ---------- |
| FTP          | 21         |
| SSH          | 22         |
| TELNET       | 23         |
| SMTP         | 25         |
| DNS          | 53         |
| TFTP         | 69         |
| HTTP         | 80         |
| SNMP         | 161        |
| SNMP（trap） | 162        |
| HTTPS        | 443        |