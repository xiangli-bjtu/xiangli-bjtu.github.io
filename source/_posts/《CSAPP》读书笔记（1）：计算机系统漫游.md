---
title: 《CSAPP》读书笔记（1）：计算机系统漫游
date: 2019-11-11 15:29:05
index_img: /img/post_index_img/CSAPP.jpg
tags: 计算机基础
categories: 技术
---

# 概述

这个系列是对于《Computer Systems: A Programmer's Perspective》（中译名为《深入理解计算机系统》，我觉得更直接的名字应该是《以程序员的视角理解计算机系统》）的读书笔记，作为计算机领域的经典书籍，每一名计算机行业的从业人员都应该去研读一下这本著作。

在b站有搬运的教学视频：[2015 CMU 15-213 CSAPP 深入理解计算机系统 课程视频](https://www.bilibili.com/video/BV1iW411d7hd)。除了观看视频，还有6个Lab也是值得去完成的，能加深你对整本书内容的理解。Lab的handout和start code可以在[CS:APP3e, Bryant and O'Hallaron](https://link.zhihu.com/?target=http%3A//csapp.cs.cmu.edu/3e/labs.html) 这里找到。

全书一共12章，第一章为对整本书的概述，主要以hello world程序的生命周期为线索，介绍了计算机系统的主要概念。剩余11章可分为3个部分：

* 2—6：程序结构和执行
* 7—9：在系统上运行程序
* 10—12：程序间的交互和通信

本系列文章的图片主要截取自教材以及b站UP主[九曲阑干](https://space.bilibili.com/354767108?spm_id_from=333.788.b_765f7570696e666f.1)的视频，以及[知乎：[读书笔记]CSAPP深入理解计算机系统](https://zhuanlan.zhihu.com/p/103476182)

# 一个hello world程序的生命周期

hello world程序作为大部分接触编程的人的第一个程序，其C语言的表示如下：

```c
#include <stdio.h>
int main(){
    printf("hello world\n");
    return 0;
}
```

这段代码需要保存在一个文件中，称为源文件（hello.c），这是这段程序生命周期的开始。

现代计算机只知道0和1的二进制数，为理解这段文本到底是什么意思，计算机系统会使用ASCII标准来表示这些文本，简单来说就是给每个字符都指定一个唯一的单字节大小的编号，然后将文本中的字符都根据ASCII标准替换成对应的编号后，就转换成了字节序列，即**源文件是以字节序列的形式保存在文件中**。对于这样只有ASCII字符构成的文件称为**文本文件**，所有其他文件都称为**二进制文件**。

使用高级的C语言编写这段程序是为了方便人理解，但是对于计算机来说太过于复杂了，它只能执行指令集中包含的指令。为了能够在系统中运行这段程序，源文件到计算机可执行的目标程序需要经历以下4个阶段：![](https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/1.png)

* **预处理**：处理源程序中以`#`开头的部分，并对源程序进行修改，比如将`#include <stdio.h>`替换成头文件`stdio.h`中的内容。
* **编译**：编译器将C语言下预处理后的`hello.i`翻译成汇编语言的`hello.s`。 这样做的好处在于：
  * 通过为不同语言不同系统上配置不同的编译器，能够提供通用的汇编语言处理结果
  * 对于相同的语言能兼容不同的操作系统，
  * 同一个系统上，通过安装不同语言的编译器，也能运行不同语言写的程序了。

汇编的过程包括词法分析、语义分析、中间代码优化等细节，汇编语言相对C语言这类高级语言也更加低级且与计算机的底层硬件密切相关。本书不会过多的展开，对这部分感兴趣的可以在读完本书后去学习编译原理

* **汇编**：将汇编程序翻译成**机器语言指令**，并将其打包成一种叫**可重定位目标程序**(relocatable object program)的二进制文件。对于微软编译器（内嵌在 Visual C++ 或者 Visual Studio 中），目标文件的后缀为`.obj`
* **链接**：链接器将各个.o文件合并成**可执行文件**。链接器使得分离编译成为可能。在编写大型程序时，通常会使用到各种函数库，但是我们代码中并没有这些函数的具体实现，所以就需要在链接阶段将该函数的具体实现合并，最终得到可执行目标文件。

# 计算机的硬件组成

经历了上一步，可执行目标文件已经在磁盘上了。那么如何运行这个程序呢，以Linux系统为例：

<img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/image-20210123112721494.png" alt="image-20210123112721494" style="zoom:67%;" />

接下来看看在运行程序时计算机系统内部发生了什么，不过在此之前我们需要了解计算机系统的硬件构成，如下图：

<img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/2.png" style="zoom: 50%;" />

1. **总线(buses)**：通常总线被设计成传送定长的字节块，也就是字。字中的字节数(字长word size)，是一个基本的系统参数，大多数机器为4个字节(32位)或8个字节(64位)
2. **I/O设备(I/O devices)**：每个I/O设备都通过一个控制器(controller)或者适配器(adapter)与I/O总线相连接。控制器和适配器的区别主要在于其封装方式：控制器是置于I/O设备本身的或主板上的芯片组，而适配器则是一块插在主板插槽上的卡
3. **主存(main memory)**：主存即内存，是一个CPU能直接寻址的临时存储设备，在处理器执行程序时用来存放程序和程序处理的数据。物理上，主存由一组DRAM组成。逻辑上，存储器是一个线性的字节数组，每一个字节都有其唯一的地址(数组索引)，这些地址均从零开始。
4. **处理器(processor)**：一个CPU由若干部分组成：
   - 寄存器：通常为8位寄存器，用来保存一个字节的数据。CPU中有若干寄存器，每个寄存器都有唯一的地址，用来保存CPU中临时运算结果。其中有两个寄存器比较特殊：
   - 指令地址寄存器：用来保存当前指令在内存中的地址，每次执行完一条指令后，会对该寄存器的值进行修改，指向下一条指令的地址。
   - 指令寄存器：用来保存当前从主存中获取的，需要执行的指令。
   - ALU：算术逻辑单元，主要用来处理CPU中的数学和逻辑运算。它包含两个二进制输入，以及一个操作码输入，用来决定对两个输入进行的算数逻辑操作。然后会输出对应的运算结果，以及具有各种标志位，比如结果是否为0、结果是否为负数等等。
   - 控制单元：是一系列门控电路，通过门控电路来判断指令寄存器中保存的指令内容，然后调整控制主存和寄存器的读写数据和地址，以及使用ALU进行运算。我的理解就是一系列门控电路，然后根据你程序的指令来调控CPU中的各种资源。

CPU中执行指令的过程：首先根据指令地址寄存器从内存中获取对应地址的数据，然后将其保存在指令寄存器中，然后控制单元会对指令内容进行判断，并调用寄存器、ALU等执行指令内容，然后更新指令地址寄存器，使其指向下一个要执行的指令地址。

**hello程序的执行过程**

可以看到即使这么一个简单的程序都需要在计算机系统内部经历复杂的交互。

<img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/3.png" style="zoom: 50%;" />



# 高速缓存

如上图的程序执行过程所示，hello程序最初存放在磁盘上，当程序加载时，被复制到内存中，当CPU运行时，指令又被复制到CPU中。这些过程会花费大量时间将代码和数据进行复制，减缓了程序“真正”的工作，程序设计的一个主要任务就是要减少信息的“搬运时间”，实现程序运行的加速。

* 关于存储设备的两条准则是：**较大的存储设备比较小的存储设备运行得慢，而高速设备的造价远高于同类的低速设备**。CPU内部的寄存器上处理器读取数据的速度要比主存快很多，并且这种差距还在持续增大。针对二者间的差距，在中间引入了**高速缓存（cache)**，用来暂时保存处理器近期可能会需要的数据，使得大部分的内存操作都能在高速缓存内完成，就能极大提高系统速度了
* 在单处理器系统中，一般含有二级缓存，最小的L1高速缓存速度几乎和访问存储器相当，大一些的L2高速缓存通过特殊总线连接到处理器，虽然比L1高速缓存慢，但是还是比直接访问主存来的快。在多核处理器中，还有一个L3高速缓存，用来共享多个核之间的数据。一般利用了高速缓存的程序会比没有使用高速缓存的程序的性能提高一个数量级。

高速缓存的思想其实不仅仅能应用于CPU中，其实对其进行扩展，就能将计算机系统中的存储设备都组织成一个存储器层次结构，如下图所示

<img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/4.jpg" style="zoom: 50%;" />

存储器层次结构的主要思想是将上一层的存储器作为下一层存储器的高速缓存。程序员可以利用对整个存储器层次结构的理解来提高程序性能。



# 操作系统

通过hello程序的运行细节，我们可以看到无论是shell程序还是hello程序都没有直接操控计算机硬件设备，**真正操控硬件的是操作系统**，它可以看成是应用程序和硬件之间的一层特殊软件。这么做的好处在于：

* 防止硬件被失控的应用程序滥用
* 给程序员提供硬件的抽象，为了这一目的，操作系统主要提供了几层的抽象：**进程(processes)**、**虚拟内存(Virtual Memory)**、**文件(Files)**

<img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/Process.png" style="zoom: 50%;" />

**进程**

为了方便对运行程序时所需的硬件进行操作，操作系统对正在运行的程序提供了一种抽象：进程。一个系统上可以同时运行多个进程，操作系统为每个进程提供了一直错觉，好像各自在独占地使用硬件。这样程序员就无需考虑程序之间切换所需操作的硬件。操作系统通过交错执行若干个程序的指令，不断地在进程间进行切换来提供这种错觉，这个称为**并发运行**。

现代系统中，一个进程中可以并发多个线程，每条线程并行执行不同的任务，**线程是操作系统能够进行运算调动的最小单位**，是进程中的实际运作单位，相比于进程：1）多线程之间比多进程之间更容易共享数据。2）线程一般来说都比进程更高效。

**虚拟内存**

将多个程序的指令和数据保存在内存中，当某个程序的数据增时，可能不会保存在内存的连续地址中，这就使得代码需要对这些在内存中非连续存储的数据进行读取，会造成很大的困难。为了解决这个问题，操作系统对内存和I/O设备进行抽象（即在你电脑的物理内存不够用时把一部分硬盘空间作为内存来使用）。程序运行在从0开始的连续虚拟内存空间中，而操作系统负责将程序的虚拟内存地址投影到对应的真实物理内存中。这样使得程序员能直接对连续的空间地址进行操作，而无需考虑非连续的物理内存地址。

操作系统将进程的虚拟内存划分为多个区域，每个区域都有自己的功能，接下来自下而上介绍：

* 程序代码和数据：对于所有进程来说，代码都是从同一固定地址开始，紧接着是全局变量相对应的数据位置。代码和数据区在进程一开始运行时就被规划了大小。
* 堆(Heap)：如C语言中调用`malloc`和`free`时，堆可以在运行时动态的扩展和收缩
* 共享库(Shared libraries)：存放像C标准库和数学库这样共享库的代码和数据的区域
* 栈(Stack)：编译器用它来实现函数调用。当调用一个函数时，栈增长；从一个函数返回时，栈收缩
* 内存虚拟存储器(Kernel virtual memory)：内核总是驻留在内存当中，是操作系统的一部分。该区域**不允许**应用程序读写或者直接调用其中的函数。

<img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/7.png" style="zoom:50%;" />

**文件**

Linux的哲学是：一切皆文件。所有I/O设备都可视作文件，系统中所有的输入输出都视作读取文件的操作。

# 网络通信

书中这部分的例子：

![](https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/8.png)

# 提高系统运行

首先引出了系统加速比的推导（Almdahl's law）如下：该定律提供的一个主要观点是**：**要想显著加速整个系统，必须提升全系统中相当大的部分的速度。

<img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/Amdahl%20law.png" style="zoom:67%;" />

为了实现系统加速，主要可通过以下3种途径：

* 线程级并发
* 指令级并行

* 单指令、多数据并行







