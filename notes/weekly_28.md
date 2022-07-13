
# Weekly 28

## 2022-07-11 Mon

### [rCore实验 第二章](https://learningos.github.io/rust-based-os-comp2022/chapter2/index.html)

#### 学习 [特权级机制](https://rcore-os.github.io/rCore-Tutorial-Book-v3/chapter2/1rv-privilege.html)

##### 特权级机制

实现特权级机制的根本原因: 应用程序运行的安全性不可充分信任.

对应用程序而言，需要限制的主要有两个方面:

- 应用程序不能访问任意的地址空间 (第四章)
- 应用程序不能执行某些可能破坏计算机系统的指令 (本章)

特权级机制需要软硬件协同设计。

具备特权级保护检查的新的机器指令:

- ecall: 具有用户态到内核态的执行环境切换能力的函数调用指令
- eret: 具有内核态到用户态的执行环境切换能力的函数返回指令

硬件具有了这样的机制后，还需要操作系统的配合才能最终完成对操作系统自身的保护。

RISC-V 特权级架构, 共定义了4种特权级. 除 M 外, 其他特权级均按需实现.

![执行环境栈与特权级](../assets/PrivilegeStack.png){width=100}

执行环境的另一种功能是对上层软件的执行进行监控管理. 常规控制流与 **异常控制流**.

RISC-V的特权指令相关:

与特权级无关的一般的指令和通用寄存器 x0 ~ x31 在任何特权级都可以执行。

如果处于低特权级状态的处理器执行了高特权级的指令，会产生非法指令错误的异常。在 RISC-V 中，会有两类属于高特权级 S 模式的特权指令：

- 指令本身属于高特权级的指令
- 指令访问了 S模式特权级下才能访问的寄存器 或内存

##### 实现应用程序

`asm!` 宏可以帮助我们更方便地和寄存器打交道, 实现内嵌汇编, 进而实现 ecall 调用的封装.

##### 实现批处理操作系统

##### 实现特权级的切换

在 RISC-V 架构中，关于 Trap 有一条重要的规则：在 Trap 前的特权级不会高于 Trap 后的特权级。

只要是 Trap 到 S 特权级，操作系统就会使用 S 特权级中与 Trap 相关的 控制状态寄存器 (CSR, Control and Status Register) 来辅助 Trap 处理。

其中, `sstatus` 是 S 特权级最重要的 CSR.

特权级切换的具体过程一部分由硬件直接完成，另一部分则需要由操作系统来实现。

- 当 CPU 执行完一条指令（如 ecall ）并准备从用户特权级 陷入（ Trap ）到 S 特权级的时候，**硬件** 会自动完成如下这些事情：......
- 而当 CPU 完成 Trap 处理准备返回的时候，需要通过一条 S 特权级的特权指令 sret 来完成，这一条指令具体完成以下功能：......

感觉和回调函数机制差不多~

用户栈与内核栈: 使用两个不同的栈主要是为了安全性. (这个是操作系统的一个设计, 并不是强制要求)

## 2022-07-13 Wed

### [rCore实验 第一章](https://learningos.github.io/rust-based-os-comp2022/chapter1/index.html)

把实验跑通. 修复了几个 `make test2` 相关的编译问题.

- `asm!` 需要改为 `core::arch::asm!`
- 在 riscv/src/lib.rs 添加 `#![feature(asm_const)]`.

```
error[E0658]: const operands for inline assembly are unstable
  --> /home/vagrant/workspaces/eastfisher/rust-based-os-comp2022/workplace/ci-user/riscv/src/register/macros.rs:12:72
   |
12 |                     core::arch::asm!("csrrs {0}, {1}, x0", out(reg) r, const $csr_number);
   |                                                                        ^^^^^^^^^^^^^^^^^
   |
  ::: /home/vagrant/workspaces/eastfisher/rust-based-os-comp2022/workplace/ci-user/riscv/src/register/hypervisorx64/vstvec.rs:41:1
   |
41 | read_csr_as!(Vstvec, 517, __read_vstvec);
   | ---------------------------------------- in this macro invocation
   |
   = note: see issue #93332 <https://github.com/rust-lang/rust/issues/93332> for more information
   = help: add `#![feature(asm_const)]` to the crate attributes to enable
   = note: this error originates in the macro `read_csr` (in Nightly builds, run with -Z macro-backtrace for more info)
```

### [rCore实验 第三章](https://learningos.github.io/rust-based-os-comp2022/chapter3/index.html)

#### 引言

> 批处理与多道程序的区别是什么？支持多道程序的系统可以交错地执行多个程序，这样系统的利用率会更高。

**Question: 为什么系统利用率会更高? 是因为可能涉及不占用CPU的IO操作吗?**

Answer: 当程序访问 I/O 外设或睡眠时，其实是不需要占用处理器的，于是我们可以把应用程序在不同时间段的执行过程分为两类，占用处理器执行有效任务的计算阶段和不必占用处理器的等待阶段

---

本章的重点是实现对应用之间的协作式和抢占式任务切换的操作系统支持。与上一章的操作系统实现相比，有如下一些不同的情况导致实现上也有差异：

- 多个应用同时放在内存中，所以他们的起始地址是不同的，且地址范围不能重叠
- 应用在整个执行过程中会暂停或被抢占，即会有主动或被动的任务切换

#### 多道程序放置与加载

在 `ci-user/user/build.py` 里面, 对 chapter3 的每个app会设置不同的 base_address:

```python
base_address = 0x80400000
step = 0x20000
app_id = 0

for app in apps:
    app = app[: app.find(".")]
    os.system(
        "cargo rustc --bin %s --release -- -Clink-args=-Ttext=%x"
        % (app, base_address + step * app_id)
    )
    print(
        "[build.py] application %s start with address %s"
        % (app, hex(base_address + step * app_id))
    )
    # 仅对 chapter3 生效, 使每个应用的 base_address 偏移 0x20000
    if chapter == '3':
        app_id = app_id + 1
```

#### 任务切换

关键概念: **任务, 任务切换, 任务上下文**

- 应用程序: 静态的应用
- 任务: 应用程序的一次执行过程 (动态)
- 任务片: 应用执行过程中的一个时间片段. 当应用程序的所有任务片都完成后，应用程序的一次任务也就完成了.
- 任务切换: 从一个程序的任务切换到另外一个程序的任务
- 任务上下文: 任务切换过程中需要保存与恢复的资源 (有被覆盖风险的那些资源)

---

不同的上下文切换:

- 普通控制流切换 (函数调用)
- Trap控制流切换
- 任务切换

重点: **`__switch` 函数** -> 任务切换的设计与实现

