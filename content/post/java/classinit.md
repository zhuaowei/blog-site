---
title: "类初始化及创建过程中，代码的执行顺序"
date: 2022-09-30T15:17:44+08:00
draft: false

tags: ["java"]
categories: ""
description: ""
---

尚硅谷Java面试题第一季第03节

## 一、代码
> Father.java
```
public class Father {
    private int i = test();
    private static int j = method();

    static {
        System.out.println("(1)");
    }

    Father() {
        System.out.println("(2)");
    }

    {
        System.out.println("(3)");
    }

    public int test() {
        System.out.println("(4)");
        return 1;
    }
    public static int method() {
        System.out.println("(5)");
        return 1;
    }
}
```

> Son.java

```
public class Son extends Father {
    private int i = test();
    private static int j = method();

    static {
        System.out.println("(6)");
    }

    Son() {
        System.out.println("(7)");
    }
    {
        System.out.println("(8)");
    }
    @Override
    public int test() {
        System.out.println("(9)");
        return 1;
    }
    public static int method() {
        System.out.println("(10)");
        return 1;
    }

    public static void main(String[] args) {
        Son s1 = new Son();
        System.out.println();
        Son s2 = new Son();
    }
}
```

结果是：(5)(1)(10)(6)(9)(3)(2)(9)(8)(7) (9)(3)(2)(9)(8)(7)

## 二、结果分析

`main` 方法中调用了两次 `new Son()`，总共发生了一次类初始化和两次对象初始化。

### 2.1、类初始化

类初始化是当类遇到需要初始化的时候根据包名将类进行加载、连接、初始化的过程，而连接又分为验证、准备、解析三个阶段。

关于类加载的条件《Java虚拟机规范》值给出了6中必须加载的情况，其中之一就是上面的 `new` 关键字。其实 `new` 关键字在解析为字节码文件后生成了new字节码指令，这个才是具体的需要加载的条件。

1. `main()` 方法所在的类需要先初始化

2. 当一个类的需要初始化时，会先检查其父类有没有初始化，如果没有，就先初始化父类。

3. 在类初始化过程中，类加载器会执行 `<clinit>` 方法，这个方法是java帮我们自动生成的，它包含类中所有静态变量的赋值代码和静态块，这些代码是按照上下文的顺序执行的。

因此首先初始化 `main()` 方法所在的 `Son` 类，然而 `Son` 类的父类还没有初始化，所以先初始化 `Father` 类。

```
1. Father.<clinit>
    j = method(); // (5)
    System.out.println("(1)"); // (1)
2. Son.<clinit>
    j = method(); (10)
    System.out.println("(6)"); // (6)
```

### 2.2、对象初始化

类初始化完毕后就是对象初始化，对象初始化也有 `<init>` 方法，类中有几个构造函数就有几个 `<init>` 方法，创建对象实例时调用对应的 `<init>` 方法。

`<init>` 方法的内容：
1. `spuer.<init>`（最前）：首先它包含父类的构造方法，无论写或不写都会有，它放在子类构造器的最前面。

2. 非静态的变量赋值代码

3. 非静态代码块

4. 子类构造器中的内容（最后）

2和3按代码书写顺序执行。

所以创建子类实例时，先执行父类的 `<init>` 方法，再执行子类的 `<init>` 方法。

```
1. Father.<init>
    i = test(); // 9，因为子类重写了父类的方法，所以执行的是子类的test()方法
    System.out.println("(3)"); // 3
    Father(); // 2
2. Son.<init>
    i = test(); // 9
    System.out.println("(8)"); // 8
    Son(); // 7
```

创建对象实例时，执行类中非静态方法的前面都有一个关键字 `this`，指代的就是子类对象本身，当初始化父类对象时，`test()` 执行的时被子类重写的 `test()`。

所以结果是：(9)(3)(2)(9)(8)(7)

### 2.3、子类继承和重写

哪些方法不可以被重写：

1. `final` 修饰的方法

2. 静态方法

3. `private` 等子类中不可见的方法

方法的多态：

1. 如果子类重写了父类的方法，通过子类对象调用时，调用的就是重写后的代码。

2. 非静态方法默认的调用对象是 `this`。

3. this在构造器或者 `<init>` 方法中就是构造的对象本身。