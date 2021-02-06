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
The number of bytes in a word (the word size) is a fundamental system parameter that varies across systems. Most machines today have word sizes of either 4 bytes (32 bits) or 8 bytes (64 bits).
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

At its core is a word-size storage device (or register) called the program counter (PC).
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

The register file is a small storage device that consists of a collection of word-size registers, each with its own unique name. 
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

The operating system keeps track of all the state information that the process needs in order to run. This state, which is known as the context,
