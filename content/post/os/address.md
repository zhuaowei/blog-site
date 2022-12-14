---
title: "操作系统中CPU的寻址方式总结"
date: 2022-09-25T20:56:50+08:00
draft: false

tags: ["计算机操作系统", "CPU寻址"]
categories: ""
description: ""
---

**前言**

今天复习计算机操作系统，网上的博客只提了一嘴，没有详细写，所以我就找到以前的课本重新复习了一遍，顺便记下来，以免忘记。详细参考了《计算机组成原理（第3版）蒋本珊 编著》。

所谓寻址，就是找到操作数的地址或者下一条指令的地址。首先先看一下编址的方式。

## 一、计算机的编址方式

编址就是对计算机的各种存储设备进行编码，这些设备主要就是各种寄存器和内存。

编址就像将存储设备中的一个个小的存储单元按一定的规则编号，根据单元的大小就有不同的编址方式。目前常用的编址方式有字编址、字节编址和位编址。

1.  字编址：字就是参与运算的最基本的数， 由加法器、寄存器的位数决定。因此，每执行一条指令，程序计数器就加1，所以这种编址方式最为简单。如果寄存器是32位的，那么一个字就是32位。

2. 字节编址：最普遍的编址方式，一个字节为一个编址单位。如果指令是32位的，那么每执行一条指令，程序计数器就会加4。

> 思考：为什么32位的计算机最大内存不超过4GB？
> 
> 现在大多数计算机使用的编址方式是按字节编址，32位可以标识的最大的数就是 $2 ^ {32}$ ，一个编址单位是 1 字节，也就是 1B ，32位最多能表示 $2 ^ {32}$ B ，也就是 4GB。目前绝大部分计算机的内存最少也是8GB，所以这些计算机肯定是64位的。

3. 按位编址：一个比特位一个编址单位，这种编址方式只有巨型计算机才会用，效率比按字节编址更低。

## 二、寻址类型和寻址方式

寻址类型可以根据要寻的目标分为数据寻址和指令寻址。

寻址方式可大致分为顺序寻址和跳跃寻址。

1. 顺序寻址：程序计数器自动加1。

2. 跳跃寻址：通过程序转移类指令来实现。根据转移的方式又可分为3种：

    a. 直接寻址

    b. 间接寻址

    c. 相对寻址

## 三、寻址方式

真是千呼万唤始出来啊，终于到了详细的寻址方式了。

1. 立即寻址：指令中直接饱含了操作数，这个数叫作立即数。优点是可以直接取数，速度快。缺点是在指令中，无法修改，也受指令位数的限制。

2. 寄存器寻址：指令中存放着寄存器的地址，通过这个地址在相应的寄存器中可以找到操作数。

3. 直接寻址：指令中存放的是操作数在内存中的地址。

4. 间接寻址：指令中存放的是操作数在内存中地址的地址。就是拿着地址从内存中找到一个地址，再用这个地址在内存中找到操作数。

> 这个间接寻址还可以套娃，上面的在指令和操作数中间隔了一次寻址，叫作一级间接寻址，还有二级、三级。也不会套很多层，因为很麻烦。

间接寻址可以让指令中的寻址范围扩大。因为指令中存放着指令和地址，地址位数不足以查找整个内存的，只能访问一部分数据，而这一部分数据可以寻址的范围是整个内存，所以寻址的范围扩大了。

5. 寄存器间接寻址：跟上面的一级间接寻址类似，不过中间地址是存放在寄存器中的。

6. 变址寻址：把变址寄存器中的地址与指令中的形式地址相加，就得到了操作数在内存中的地址。适合顺序存储的数组和字符串等。

7. 基址寻址：与变址寻址类似，是将基址寄存器中的地址与指令中的位移量相加得出操作数的有效地址。

> 变址寻址和基址寻址有何区别？
>
> 1. 变址寻址是变址寄存器提供的修改量，是可变的，指令中提供的基准值是固定的；而基址寄存器提供的基准值是不可变的，指令中提供的位移量是可变的。
> 
> 2. 变址寻址面向的是用户，用于数组、字符串和向量等批数据；基址寻址面向的是系统，主要用于逻辑地址和物理地址的变换等。

8. 相对寻址：相对寻址是基址寻址的一种变通，它是由PC提供基准值，指令提供位移量，这个位移量可正可负，所以最终寻址可能在基准值的前或者后。

9. 页面寻址：将主存分为若干个大小相同的区，也叫页，每个页再分为若干个大小相同的主存单元，每个页都有自己的编号，叫页面地址；页内每个主存单元也有自己的编号，叫作页内地址。例如64KB的内存空间被分为256个页面，每个页面中又有256个主存单元。寻址时，用前8位表示页面地址，后8位表示页内地址，两者一拼凑就行成了内存地址。

页面寻址的页内地址一般直接来源于指令，而根据页面地址的不同可以将页面寻址再次分为3种：

    a. 基页寻址：也叫零页寻址，因为页面地址全是0，所以操作数在零页面中，相当于直接寻址。

    b. 当前页寻址：页面地址就等于PC的高位部分的内容，操作数与指令本身处于同一页面中。

    c. 页寄存器寻址：页面地址取自页寄存器。

除了这9中寻址方式之外，还有几种更为复杂的寻址方式，其实就是这9中的变形或组合。计算机中寻址方式不只一种，一般是几种寻址方式互相组合来达到更高的效率。