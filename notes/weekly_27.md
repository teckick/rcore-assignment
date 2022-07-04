# Weekly 27

## 20220704 Mon

### 搭建实验环境

按照教程创建 Github Classroom 仓库.

地址: https://github.com/LearningOS/lab0-0-setup-env-run-os1-eastfisher

在本地 MacOS 环境使用 Vagrant 跑通 [lab0](https://learningos.github.io/rust-based-os-comp2022/0setup-devel-env.html).

### Rustlings

将之前自己 Fork 的仓库切换成使用 Github Classroom 仓库.

地址: https://github.com/LearningOS/learn_rust_rustlings-eastfisher

练习 Rustlings, 进度 42/84 .

### Rust语言圣经

继续学习Rust语言圣经, 章节 3.3 ~ 3.6, 有一些章节需要重点看看:

#### [3.3.2 Sized 和不定长类型 DST](https://course.rs/advance/into-types/sized.html)

`Box<str>` 这里还是有点疑问, 为什么以下这种方式能够通过编译:

```rust
let s1: Box<str> = "Hello there!".into();
```

#### [3.4.2 Deref 解引用](https://course.rs/advance/smart-pointer/deref.html)

#### [3.6.6 基于 Send 和 Sync 的线程安全](https://course.rs/advance/concurrency-with-threads/send-sync.html)

- 实现Send的类型可以在线程间安全的传递其所有权
- 实现Sync的类型可以在线程间安全的共享(通过引用)

这里还有一个潜在的依赖：一个类型要在线程间安全的共享的前提是，指向它的引用必须能在线程间传递。

由上可知，若类型 T 的引用&T是Send，则T是Sync。
