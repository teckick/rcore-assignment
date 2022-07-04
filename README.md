# Assignment for Rust Based OS Comp 2022

## 简介

本仓库记录了我在 [2022年开源操作系统训练营](https://github.com/LearningOS/rust-based-os-comp2022) 的整个学习过程.

时间: 2022年7月1日～2022年9月10日

课程目标: 探索把现代系统语言Rust和灵活开放的系统结构RISC-V带入到操作系统的架构与设计的创新中来，思考未来的操作系统应该是什么样。

## 学习记录

- [Weekly 26 (2022/06/29 ~ 2022/07/03)](notes/weekly_26.md)
- [Weekly 27 (2022/07/04 ~ 2022/07/10)](notes/weekly_27.md)

## To-Do List

### Part 1

#### step 0 自学rust编程（大约7~14天）

- [ ] [Small exercises to get you used to reading and writing Rust code!](https://github.com/rust-lang/rustlings)
  - [ ] 要求：小练习全部通过。
  - [ ] 代码和README提交在自己在github的公开repo上。
- [ ] [32 Rust Quizes](https://dtolnay.github.io/rust-quiz/1)
  - [ ] 要求：小练习全部通过。
- [ ] [exercisms.io 快速练习(88+道题目的中文详细描述)](http://llever.com/exercism-rust-zh/index.html)
  - [ ] 要求：大部分练习会做或能读懂。

#### step 1 自学risc-v系统结构（大约7天）

- [ ] 阅读《计算机组成与设计（RISC-V版）》第一、二章
- [ ] 自学[PPT for RISC-V特权指令级架构](https://content.riscv.org/wp-content/uploads/2018/05/riscv-privileged-BCN.v7-2.pdf)
- [ ] 自学[RISC-V手册：一本开源指令集的指南](http://crva.io/documents/RISC-V-Reader-Chinese-v2p1.pdf) 重点是第10章
- [ ] 自学[RIS-V特权指令级规范](https://riscv.org/specifications/privileged-isa/) 重点是与OS相关的特权硬件访问的内容
- [ ] [计算机组成与设计：RISC-V 教材](https://item.jd.com/12887758.html) 这是完整的课程教材，不要求全部看完，请根据自己的需求选择。
- [ ] [计算机组成与设计：RISC-V 浙大在线课程](http://www.icourse163.org/course/ZJU-1452997167) 这是完整的一门课，不要求全部看完，请根据自己的需求选择。
- [ ] [Berkeley CS61C: Great Ideas in Computer Architecture (Machine Structures)](http://www-inst.eecs.berkeley.edu/~cs61c/sp18/)

#### step 2 基于Rust语言进行操作系统内核实验--based on qemu （大约14~31天）

- [ ] 学习理解[OS课程slides](https://learningos.github.io/os-lectures/)
- [ ] 学习了解 [rCore Tutorial v3的详细实验指导内容](https://rcore-os.github.io/rCore-Tutorial-Book-v3/)
- [ ] 学习理解[rCore Tutorial v3的实验代码](https://github.com/rcore-os/rCore-Tutorial-v3)
  - 2022年OS课程视频：请向组织者申请
- [ ] 根据[rust-based-os-comp2022](https://github.com/LearningOS/rust-based-os-comp2022)中的[具体实验要求](https://learningos.github.io/rCore-Tutorial-Guide-2022S/)在自己的仓库中完成5个实验，并通过测试。

### Part 2

TODO

[stage2-sched](https://github.com/LearningOS/rust-based-os-comp2022/blob/main/stage2-sched.md)
