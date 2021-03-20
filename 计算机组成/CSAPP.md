# Chapter 1 A Tour of Computer Systems  章一 计算机系统漫游
A computer system consists of hardware and systems software that work to- gether to run application programs.
计算机系统 是由 软件 和 硬件组成的

## 1.1 Information Is Bits + Context 信息就是位+上下文
Our hello program begins life as a source program (or source file) that the programmer creates with an editor and saves in a text file called hello.c. 

The source program is a **sequence of bits**, each with a value of 0 or 1, organized in 8-bit **chunks** called **bytes**. 
source program 是 一个 由0和1组成的 位 的 **序列** ， 每8个 bit 被组织成一组 称为 **字节**

Each byte represents some text character in the program. 每个字节表示程序中的某些 文本字符 （注意是字节 byte 而非 位 bit）

Most computer systems represent text characters using the ASCII standard that represents each character with a unique byte-size integer value.
ASCII，唯一的 单字节大小的整数值来表示每个字符 8-bit = bytes

The hello.c program is stored in a file as a sequence of bytes.  
hello.c program 是以 **字节序列(a sequence of bytes)** 方式存储在文件中的 

Files such as hello.c that consist exclusively of ASCII characters are known as text files. All other files are known as binary files.
像 hello.c 这样的 纯由 **ASCII字符 构成的成为文本文件（text files） 其他的都叫二进制文件 binary files**（存储都是由bits 嘛）


The representation of hello.c illustrates（考研单词 ： v. 给…加插图 说明, 阐明; 表明） a fundamental idea: 
All information in a system—including disk files （磁盘文件）, programs stored in memory（内存中的程序）, user data stored in memory（内存中存放的用户数据）, and data transferred across a network（网络上传送的数据）—is **represented as a bunch of bits**.
系统中所有的信息都是由一串比特表示的

The only thing that distinguishes different data objects is the **context** in which we view them. 
唯一区分不同数据对象的方法是 ： 我们读取到的 **context 上下文**

For example, in different contexts, the same sequence of bytes might represent an integer, floating-point number, character string, or machine instruction.

## 1.2 Programs Are Translated by Other Programs into Different Forms 程序被其他程序翻译成不同的格式
The hello program begins life as a high-level C program because it can be read and understood by human beings in that form. 

However, in order to run hello.c on the system, the individual C statements must be translated by other programs into a sequence of low-level machine-language instructions. 
为了在系统上运行 hello.c 每条C语句都要被其他程序转化为一系列的低级 machine-languag 指令

These instructions are then packaged in a form called an **executable object program 可执行目标程序** **and stored as a binary disk file 存储成二级制磁盘文件，也是上文说的二进制文件 binary files**. 

Object programs are also referred to as executable object files.
目标程序 也被称为 可执行目标程序 （下文都叫了目标程序，这个意思是让你不要混淆）

On a Unix system, the translation from source file to object file is performed by a **compiler driver编译器驱动程序（编译程序）**

The **programs 这里说的是程序** that perform the four phases (**preprocessor预处理程序 cpp, compiler编译程序cc1, assembler汇编程序as, and linker链接程序ld （翻译版写的是xx器，都说了是程序了**) are known collectively as the **compilation system编译系统**

- **Reprocessing phase(预处理阶段)**.The preprocessor (cpp) modifies the original C program according to directives（n. 正式的指示,官方的指示;指令） that begin with the ‘#’ character. For example, the #include <stdio.h> command in line 1 of hello.c tells the preprocessor to read the contents of the system header file stdio.h and **insert it directly into the program text直接插入程序文本中**. The result is another C program, typically with the `.i` suffix. 
**注意这时候后缀变`.i` 程序还是所谓的"text file 文本文件"**
- **Compilation phase编译阶段**. The compiler (cc1) translates the text file `hello.i` into the text file `hello.s`, which contains an **assembly-language program 汇编语言程序**. 
**这时候后缀变`.s` 程序还是 text file 但是得到的是 汇编语言程序**
- **Assembly phase汇编阶段**. Next, the assembler (as) translates hello.s into **machine-language** instructions, packages them in a form known as a **relocatable object program 翻译成机器语言，打包成了 可重定位目标程序**, and stores the result in the object file **hello.o**. This file is a **binary file** containing 17 bytes to encode the instructions for function main. If we were to view hello.o with a text editor, it would appear to be gibberish.
**这时候后缀变`.o` 程序就成了binary file**
- **Linkingphase链接阶段**.Noticethatour hello program calls the printf function, which is part of the standard C library provided by every C compiler. The printf function resides in a separate precompiled object file called **printf.o 注意是.o 上面说经过第三阶段：汇编才会产生.o**, which must somehow be merged with our hello.o program**这个printf.o得和我们的hello.o合并**. The linker (ld) handles this merging**链接程序ld 就做这的**. The result is **the hello file 结果得到是一个file,**, which is an executable object file (or simply executable) that is ready to be loaded into memory and executed by the system.

## 1.3 It Pays to Understand How Compilation Systems Work 了解编译系如何工作是大有益处的 （It Pays to... 是有好处的）
**这里先不看 考研重要**
**U1S1 这里的东西才有意思的**

## 1.4 Processors  /Read and Interpret / Instructions Stored in Memory 

### 1.4.1 系统的硬件组成

#### Buses 总线 （你妈的谁翻译的总线）
Running throughout the system is a collection of electrical conduits called buses。
贯穿系统的 一组 电子管道 被叫做总线
that** carry bytes** of information **back and forth反复地，来回地** between the components.
总线来回carry byte**s** 在各个组件中
Buses are typically designed to transfer **fixed-size 固定尺寸** **chunks of bytes** known as **words 字**.
总线被设计成 传送 固定尺寸的字节块 ，也就是字
The number of bytes in a **word (the word size)** is a fundamental system parameter that varies across systems. Most machines today have word sizes of either 4 bytes (32 bits) or 8 bytes (64 bits).
字的字节数（字长）是一个基本的系统参数，（一般要么是4字节（32w位）或者8字节（64位））

#### I/O Devices I/O 设备
Each I/O device is connected to the I/O bus by either a **controller控制器** or an **adapter适配器**.
每个I/O设备都通过过一个控制器或者适配器与I/O总线相连

#### Main Memory 主存
The main memory is a **temporary storage device临时存储设备** that holds both a program and the data it manipulates while the processor（CPU） is executing the program. 
存储的是 processor 需要的程序和数据(这里翻译的可能不太好
)

Physically,main memory consists of a collection of d**ynamic random access memory (DRAM)动态随机存取器** chips. 
物理上来说 主存是一组DRAM 
Logically, memory is organized as **a linear array of bytes**, each with its own **unique address (array index)** starting at zero. 
逻辑上来说，主存是 一个线性的字节（bytes）数组，每个字节都有其唯一的地址

####  Processor
the engine that interprets解释 (or executes执行) instructions stored in main memory. 
是解释/执行存储在主存中指令的引擎

At its core is a **word-size** storage device (or register) called the program counter (PC).
CPU的核心是一个大小为一个字的存储设备（寄存器） 叫做 PC 程序计数器

processor repeatedly executes **the instruction pointed at by the program counte**r and updates the program counter to point to the next instructions
处理器一直不停的执行PC指向的指令，以及更新PC使得它指向下一条指令

A processor appears to operate according to a very simple instruction execution model, defined by its **instruction set architecture指令集架构**. 

In this model, instructions execute in strict sequence, and executing a single instruction involves performing a seriesof steps. 
指令按照严格的顺序执行，而执行一条指令包含一系列的步骤

The processor reads the instruction from memory pointed at by theprogram counter (PC)
处理器从PC指向的内存读取指令

interprets the bits in the instruction, 
解释指令中的bits位

performs some simple operation dictated by the instruction,
执行该指令知识的简单操作

and then updates the PC to point to the next instruction, which may or may not be contiguous（adj. 接触的,邻近的,共同的） in memory to the instruction that was just executed.
然后更新PC，使其指向下一条指令（这条新指令不一定在内存中与刚刚执行的指令相邻）

There are only a few of these simple operations, and they revolve around main memory主存, the **register file寄存器文件**, and the **arithmetic/logic unit (ALU)算术/逻辑单元**. 

The register file is a small storage device that consists of a collection of **word-size** registers, each with its own unique name. 
寄存器文件是一个小的存储设备，由一系列的 单个字长 的寄存器组成，每个寄存器都有其独特的名称。

The ALU computes new data and address values.
ALU会计算新的数据和地址值


the **processor’s instruction set architecture处理器的指令架构**, describing the effect of each machine-codeinstruction,
指令集架构 描述 每条机器代码指令的效果

from its microarchitecture**微体系结构**, describing how the processor is actuallyimplemented.
微体系结构 描述 处理器实际上是如何实现的

### 1.4.2 Running the hello program

shell执行指令，等待我们输入命令，当键盘上输入字符串"./hello"，shell程序将字符逐一读入寄存器，再把它放入内存中

当我们按下回车，sheel知道结束了命令的输入，然后sheel执行一系列指令来执行 the executable hello file ，这些指令将 the executable hello file 中的代码和数据从次哦按复制到主存。

Using a technique known as **direct memory access 直接存储器读取 (DMA)**, the data travel directly **from disk to main memory**, **without passing through the processor.**

Once the code and data in the hello object file are loaded into memory,the processor begins executing the machine-language instructions in the hello program’s main routine. These instructions copy the bytes in the "hello, world\n" string from memory to the register file, and from there to the display device, where they are displayed on the screen. 
一旦目标文件hello中的代码和数据被加载到主存，处理器就开始执行hello程序的main程序中的机器语言指令。这些指令将"hello, world\n"字符串中的字节从主存复制到寄存器文件，再从寄存器文件中复制到显示设备，最终显示在屏幕上。

## 1.5 Caches Matter 

**static random access memory(SRAM).静态随机访问存储器**

重要的是图

## 1.6 Storage Devices Form a Hierarchy 存储设备形成层次结构
一张图就概况了
TODO:picgo

## 1.7 The Operating System Manages the Hardware 操作系统管理硬件
All attempts by an application program to manipulate the hardware must go through the operating system.
所有的应用程序 对硬件的 操作尝试 都**必须通过操作系统**

The operating system has two primary purposes:
(1) to protect the hardware from misuse by runaway applications and 
保护硬件被失控的程序误用
 
(2) to provide applications with simple and uniform mechanisms for manipulating complicated and often wildly different low-level hardware devices.
向应用程序提供简单一致的机制来控制复杂又通常不大相同的低级的硬件设备

The operating system achieves both goals via the fundamental abstractions shown in Figure 1.11: **processes进程, virtual memory虚拟内存, and files文件.**

files are abstractions for I/O devices,
文件是 I/O 设备的抽象表示

virtual memory is an abstraction for both the main memory and disk I/O devices, 
虚拟内存是 主存和磁盘I/O 设备的抽象表示

and processes are abstractions for the processor, main memory, and I/O devices. 
进程是 处理器 主存 和 I/O 设备的抽象表示

### 1.7.1 Processes 进程
A process is the operating system’s abstraction for a running program.
进程是OS对一个正在运行的程序的抽象

The operating system performs this interleaving with a mechanism known as **context switching 上下文切换**. 
OS实现 这种交错的机制 叫做 上下文切换

The operating system keeps track of all the state information that the process needs in order to run. This state, which is known as the **context**,
OS持续追踪进程所需要的所有的状态信息（上下文）

When the operating system decides to transfer control from the current process to some new process, it performs a **context switch** by saving the context of the current process, restoring the context of the new process, and then passing control to the new process. The new process picks up exactly where it left off. 
当OS决定把控制权从当前进程转移到新进程，会进行**上下文切换**：保存当前进程的上下文，恢复新进程的上下文，然后将控制权交给新进程，新进程会从它上次停止的地方开始

**这中间用sheell和hello进行了举例，但是我没写，因为我会（笑）**

the transition from one process to another is managed by the **operating system kernel**. 
从一个进程到另一个进程的转换是由操作系统内核**实现**的

The kernel is the portion of the operating system code that is always resident in memory. 
内核是 OS代码常驻内存的的部分

When an application program requires some action by the operating system, such as to read or write a file, it executes a special **system call** instruction, transferring control to the kernel. The kernel then performs the requested operation and returns back to the application program. 
当应用程序需要操作系统的某些操作时，比如读写文件，它就执行一条特殊的系统调用(system call)指令，将控制权传递给内核。然后内核执行被请求的操作并返回应用程序。

Note that the kernel is not a separate process. Instead, it is a collection of code and data structures that the system uses to manage all the processes.
注意：内核不是一个独立的进程，而是一个系统管理所有进程的所需要的代码和数据结果的集合

### 1.7.2 Threads

a process can actually consist of multiple execution units, called threads,each running in the context of the process and sharing the same code and global data. 
一个进程实际上可以由多个称为线程的执行单元组成，每个线程都讯息在进程的上下文中（？），并共享同样的代码和**global dat全局数据**

### 1.7.3 Virtual memory
略 这个在OS里慢慢补上

### 1.7.4 Files
A file is a sequence of bytes, nothing more and nothing less. （这话也太酷了 nothing more and nothing less 
文件，就是字节序列

Every I/O device, including disks, keyboards, displays, and even networks, is modeled as a file.
All input and output in the system is performed by reading and writing files, using a small set of system calls known as Unix I/O.
文件就是字节序列，仅此而已。每个I/O设备，包括磁盘、键盘、显示器，甚至网络，都可以看成是文件。系统中的所有输人输出都是通过使用一小组称为Unix I/O的系统函数调用读写文件来实现的。

## 1.8 Systems Communicate with Other Systems Using Networks 

## 1.9 Important Themes

### 1.9.1 Amdahl’s Law

### 1.9.2 Concurrency 并发 and Parallelism 并行

#### Thread-Level Concurrency

#### Instruction-Level Parallelism

#### Single-Instruction, Multiple-Data (SIMD) Parallelism


# Chapter 2  Representing and Manipulating Information 信息的表示和处理

本章目光放在三种表示上：

**Unsigned** encodings are based on traditional binary notation, representing numbers greater than or equal to 0. 
无符号编码，基于传统的二级制表示法，表示大于等于0的数组


**Two’s-complemen**t encodings are the most common way to represent signed integers, that is, numbers that may be either positive or negative.
补码

Floating-point encodings are a base-2 version of scientific notation for representing real numbers. 
浮点数是表示实数的以二为底的科学计数法

Computer representations use a limited number of bits to encode a number,and hence some operations can overflow when the results are too large to be represented. 
计算机的表示法，是用有限位数对一个编码，当结果太大以至于不能表示时，某些运算就会溢出

## 2.1 Information Storage  信息存储

Rather than accessing individual bits in memory, most computers use blocks of 8 bits, or bytes, as the smallest addressable unit of memory. 
大多数计算机使用 8bit 8位的块 ，或者 字节，作为**最小的可寻址的内存单位**，而不是访问内存中单独的位

A machine-level program views memory as a very large array of bytes, referred to as virtual memory. Every byte of memory is identified by a unique number, known as its address, and the set of all possible addresses is known as the virtual address space.
**机器级程序将内存视为一个非常大的字节数组，称为虚拟内存( virtual memory)。内存的每个字节都由一个唯一的数字来标识，称为它的地址(address)，所有可能地址的集合就称为虚拟地址空间( virtual address space)**

how the **compiler** and **run-time system** **partitions vt. 分开, 隔开** this memory space into more manageable units to store the different program objects, that is, program data, instructions, and control information.
接下来的几章是讲：**编译器和运行时系统** 是如何将存储器空间划分为 更多的可管理的单元，用来存放不同的程序对象：程序数据，指令和控制信息

Various mechanisms are used to allocate and manage the storage for different parts of the program. This management is all performed within the virtual address space. 
可以用各种机制来分配和管理程序不同部分的存储，这种管理完全是在虚拟地址空间完成的

### 2.1.1 Hexadecimal Notation 十六进制表示法
这里没啥，如果是背景知识的话，大概是：
一个字节是8位，用二进制是 00000000-11111111 十进制是0-255,二进制太长，十进制不好算，就用了16进制，一个字节变成了00-FF
> 顺便一提，一般我们说是 0x00-0xFF ,0x代表的是16进制

本节的重点是 二和十六进制的转换

### 2.1.2 Data Sizes
Every computer has a **word size字长**, **indicating n. 指示,标志** the **nominal** size of pointer data.
每台计算机都有字长,指明 指针数据的 标称大小
> nominal : n.a phrase that can function as the subject or object of a verb 

Since a virtual address is encoded by such a word, the most important system parameter determined by the word size is the maximum size of the virtual address space. 
因为虚拟地址是根据字长来编码的，所以**字长所决定的最重要的系统参数就是虚拟空间地址的最大值**

That is, for a machine with a w-bit word size, the virtual addresses can range from 0 to $2^w − 1$, giving the program access to at most $2^w$ bytes
对一个w位字长的机器而言，虚拟地址的范围是 0- $2^w − 1$ ,程序最多访问$w^w$个字节

以下的内容没什么...
今年来都编程64位了
gcc -m32 prog.c 
gcc -m64 prog.c 
可以编译不同的程序

We will therefore refer to programs as being either “32-bit programs” or “64-bit programs,” since the distinction lies in how a program is compiled, rather than the type of machine on which it runs.
我们称32位程序和64位程序的时候，是讲这个程序是如何编译的，而不是程序运行的机器

### 2.1.3 Addressing and Byte Ordering 寻址 和 字节顺序

For program objects that **span** multiple bytes, we must **establish 建立, 成立** two conventions:what the address of the object will be, and how we will order the bytes in memory.
对一个跨多字节的的程序来说，我们必须建立两个规则：这个对象的地址是什么，已经我们如何在内存中order这些字节

In virtually all machines, a multi-byte object is stored as a contiguous sequence
of bytes, with the address of the object given by the smallest address of the bytes
used. For example, suppose a variable x of type int has address 0x100; that is, the
value of the address expression &x is 0x100. Then (assuming data type int has a
32-bit representation) the 4 bytes of x would be stored in memory locations 0x100,
0x101, 0x102, and 0x103.


接下来讲大端派和小端派
但是U1S1 
```
4004d3:     01 05 43 0b 20 00   add     %eax,0x200b43(%rip)
``` 
这个举例我没有懂
大致意思说小端法笔记反人类，非要把最低位放左边（我寻思这是好文明啊
而且下面的C语言的强制类型转换属实没明白在干啥（我指的是在OS中的用处）
不过好歹后面做了解释，但是还是没get到为啥

### 2.1.4 Representing Strings 表示字符串

### 2.1.5 Representing Code 表示编码

### 2.1.6 Introduction to Boolean Algebra 布尔代数简介

### 2.1.7 Bit-Level Operations in C C语言中的位运算

### 2.1.8 Logical Operations in C C语言中的逻辑运算

### 2.1.9 Shift Operations in C C语言中的移位运算

## 2.2 Integer Representations 整数表示
two different ways bits can be used to encodeintegers — one that can only represent nonnegative numbers, and one that can represent negative, zero, and positive numbers. 
用位描述整数有两种不同方式：一种只能表示非负数，另一种能够表示负数，零，正数

### 2.2.1 Integral Data Types  整数数据类型
讲一讲C语言的整数数据类型
同样提出一个问题说：
the range of negative numbers extends one further than the range of positive numbers.
负数的范围要比整数的范围大一

### 2.2.2 Unsigned Encodings 无符号数的编码

### 2.2.3 Two’s-Complement Encodings 补码编码
we wish to represent negative values as well. The most common computer representation of signed numbers is known as two’s-complement form. 
我们希望表示负数值，最常见的有符号数的计算机表示方式是 补码

This is defined by interpreting the most significant bit of the word to have negative weight. 
这个定义中，将字的最高有效位定义为负权

符号位为1时，表示值为负；符号位为0时，表示值为非负（注意不是正）

### 2.2.4 Conversions between Signed and Unsigned

 
# Chapter Machine-Level Representation of Programs 章三 程序的机器级表示

