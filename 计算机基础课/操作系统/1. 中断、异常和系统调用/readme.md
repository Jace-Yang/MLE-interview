# 中断、异常和系统调用

#### 1. 计算机系统抽象层次


![img](https://pic3.zhimg.com/80/v2-d70a3e4c16eed259e24ae5f4ec7e00ed_1440w.jpeg)


其中，**指令集**是硬件/软件的边界。

操作系统kernel和CPU硬件有着很强的对应关系：

Kernel:

- 进程 -> CPU(算数逻辑单元)
- 内存 -> Memory
- 外设 -> I/O
- 文件系统 ->磁盘

#### 2. OS与应用程序的关系

应用程序不能直接调用内核的函数, 而应该通过系统调用，陷入内核来调用内核函数。![img](https://pic2.zhimg.com/80/v2-5e90ea2c6cabacab0fca20629d79ccc7_1440w.jpeg)



#### 3. 隔离

运行的程序通常是隔离的单元，防止程序X破坏、监视程序Y，或者进程干扰操作系统。

##### 3.1 主要的隔离方法：

- 地址空间。一个程序仅寻址自己的内存；若无许可不能访问不属于自己的内存。
- CPU硬件中的**特权模式**。防止应用程序访问设备和敏感的CPU寄存器。
- 中断。需要打断一直占用CPU的应用程序。

##### 3.2 特权模式

Kernel Mode 可以执行 User mode无法执行的特权操作：如访问外设、配置地址空间（虚拟内存）、读写系统级寄存器。

OS运行在Kernel Mode，应用程序运行在User mode。

##### 3.3 中断

中断异步发生，是来自处理器外部的IO设备的信号。

- IO设备通过向处理器芯片的一个引脚发信号，并将异常号放到系统总线上，以触发中断；
- 当前指令执行完之后（CPU每条指令结束之后都去检查），从系统总线读取异常号，保存现场，切换到Kernel；
- 调用中断处理程序，恢复现场。

Timer可以稳定产生中断，防止应用程序死占CPU不放。（回忆大作业内容，每个进程都只能运行一个小时间片）

#### 4. 主流CPU指令集

x86: CISC

RISC-V: RISC, 精简指令集





