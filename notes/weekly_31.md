# Weekly 31

## 2022-08-02 Tue

### [rCore实验 第五章](https://learningos.github.io/rust-based-os-comp2022/chapter5/index.html)

#### 引言

任务和进程的关系与区别。

进程模型：若想对进程进行管理，实现创建、退出等操作，核心就在于 fork/exec/waitpid 三个系统调用。

进程管理机制，包含以下几个方面：

- 初始进程的创建
- 进程切换机制
- 进程调度机制
- 进程生成机制
- 进程资源回收机制
- 进程的 I/O 输入机制

#### 进程概念及重要系统调用

相比于 任务 ， 进程 (Process) 的含义是 在操作系统管理下的程序的一次执行过程。这里的“程序的”成了形容词，而执行过程成为了主角，这充分体现了动态变化的执行特点。

进程就是操作系统选取某个可执行文件并对其进行一次动态执行的过程。

相比可执行文件，它的动态性主要体现在：

- 它是一个过程，从时间上来看有开始也有结束；
- 在该过程中对于可执行文件中给出的需求要相应对 硬件/虚拟资源 进行 动态绑定和解绑。

进程相关概念定义：

- 进程标识符：在系统中区分同一时间存在的每个进程的唯一标识。（是动态的）
- 用户初始进程：在内核初始化完毕之后会创建一个进程，它是目前在内核中以硬编码方式创建的唯一一个进程，其他进程都是通过该进程fork出来的。

重要系统调用：

- fork
  - 创建瞬间，新进程的用户态的代码段、堆栈段及其他数据段的内容完全相同，但是被放在独立的地址空间中。
  - 唯有用来保存 fork 系统调用返回值的 a0 寄存器（函数返回值所用的寄存器）的值是不同的。
  - 每个进程可能有多个子进程，但最多只能有一个父进程
  - 所有进程可以被组织成一颗树，其根节点正是代表用户初始程序——initproc
- waitpid
  - 收集该进程的返回状态并回收掉它所占据的全部资源，这样这个进程才被彻底销毁。
  - 如果一个进程先于它的子进程结束，在它退出的时候，它的所有子进程将成为进程树的根节点——用户初始进程的子进程，同时这些子进程的父进程也会转成用户初始进程。
  - 这些子进程的资源就由用户初始进程负责回收了，这也是用户初始进程很重要的一个用途。
- exec
  - 用来执行不同的可执行文件

为何创建进程要通过两个系统调用而不是一个？灵活性。

了解了 **initproc** 和 **user_shell** 这两个程序的主要功能。

#### 进程管理的核心数据结构

- 基于应用名的应用链接：在编译阶段的链接过程中，生成包含多个应用和应用位置信息的 link_app.S 文件。
- 基于应用名的加载器：根据应用名字来加载应用的 ELF 文件中代码段和数据段到内存中，为创建一个新进程做准备。
- 进程标识符 PidHandle 以及内核栈 KernelStack ：进程控制块的重要组成部分。
- 任务控制块 TaskControlBlock ：表示进程的核心数据结构。（重要）
  - 不可变部分：PidHandle 和 KernelStack。
  - 可变部分：放在 UPSafeCell<TaskControlBlockInner> 中。
- 任务管理器 TaskManager ：管理进程集合的核心数据结构。
- 处理器管理结构 Processor ：用于进程调度，维护进程的处理器状态。（从TaskManager中拆分出来）
  - 任务调度的 idle 控制流：尝试从任务管理器中选出一个任务来在当前 CPU 核上执行。

#### 进程管理机制的设计实现

- 创建初始进程：创建第一个用户态进程 initproc；
- 进程调度机制：当进程主动调用 sys_yield 交出 CPU 使用权或者内核把本轮分配的时间片用尽的进程换出且换入下一个进程；
- 进程生成机制：介绍进程相关的两个重要系统调用 sys_fork/sys_exec 的实现；
  - fork 系统调用的实现
  - exec 系统调用的实现：
    - 解析 ELF 数据
    - 从 ELF 文件生成一个全新的地址空间并直接替换进来
    - 修改新的地址空间中的 Trap 上下文，将解析得到的应用入口点、用户栈位置以及一些内核的信息进行初始化
  - 系统调用后重新获取 Trap 上下文
- 进程资源回收机制：
  - 进程的退出：应用调用 sys_exit 系统调用主动退出或者出错由内核终止之后，会在内核中调用 exit_current_and_run_next 函数退出当前进程并切换到下一个进程。
  - 父进程回收子进程资源：父进程通过 sys_waitpid 系统调用来回收子进程的资源并收集它的一些信息。
- 字符输入机制：为了支持shell程序-user_shell获得字符输入，介绍 sys_read 系统调用的实现；

### 进程调度