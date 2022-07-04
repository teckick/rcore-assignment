# Weekly 27

## 20220704 Mon

### 搭建实验环境

按照教程创建 Github Classroom 仓库.

地址: https://github.com/LearningOS/lab0-0-setup-env-run-os1-eastfisher

在本地 MacOS 环境使用 Vagrant 跑通 lab0.

https://learn.hashicorp.com/tutorials/vagrant/getting-started-project-setup?in=vagrant/getting-started

### Rustlings

将之前自己 Fork 的仓库切换成使用 Github Classroom 仓库.

地址: https://github.com/LearningOS/learn_rust_rustlings-eastfisher

练习 Rustlings, 进度 42/84 .

### Rust语言圣经

继续学习Rust语言圣经, 章节 3.3 ~ 3.10, 有一些章节需要重点看看:

#### [3.3.2 Sized 和不定长类型 DST](https://course.rs/advance/into-types/sized.html)

`Box<str>` 这里还是有点疑问, 为什么以下这种方式能够通过编译:

```rust
let s1: Box<str> = "Hello there!".into();
```

#### [3.4.2 Deref 解引用](https://course.rs/advance/smart-pointer/deref.html)

