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

## 20220704 Tue

### Rust语言圣经

继续学习Rust语言圣经, 章节 3.7 ~ 3.10, 有一些章节需要重点看看:

#### [3.8 错误处理](https://course.rs/advance/errors.html)

归一化错误类型, 通常有三种方式:

- 使用特征对象 Box<dyn Error>
- 自定义错误类型
- 使用 thiserror

关于如何选用 thiserror 和 anyhow 只需要遵循一个原则即可：是否关注自定义错误消息，关注则使用 thiserror（常见业务代码），否则使用 anyhow（编写第三方库代码）。

#### [3.10 Macro 宏编程](https://course.rs/advance/macro.html)

宏和函数的区别.

宏和函数的区别并不少，而且对于宏擅长的领域，函数其实是有些无能为力的: **元编程, 可变参数, 宏展开**.

缺点: 比函数复杂, 难以理解和维护.

### Rustlings

练习 Rustlings, 进度 ??/84 .

#### error4.rs

如何用match匹配区间? 有几种方式: https://users.rust-lang.org/t/greater-than-less-than-in-a-match-block/63399

1. cmp & Ordering
2. match中指定区间 (i32::MIN..=1 | 3..=i32::MAX => x = 2,)
3. 不用match, 用if-else

cmp时如何与字面量比较?

https://stackoverflow.com/questions/40677086/why-isnt-it-possible-to-compare-a-borrowed-integer-to-a-literal-integer

> You interpret the error correctly, and the reason is that it simply isn't implemented. If the standard library writers wanted to make this work, they'd have to implement PartialEq for &i32 == i32, i32 == &i32, &mut i32 == i32, i32 == &mut i32, &i32 == &mut i32 and &mut i32 == &i32. And then they'd have to do that for all other primitive types (i8, i16, u8, u16, u32, i64, u64, f32, f64, and char).

这里遇到的问题是, &i64 与字面量 0 比较时, 0 需要写成 `&(0 as i64)`.
