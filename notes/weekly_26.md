# Weekly 26

计划用第1周和第2周完整学习 Rust 的核心部分, 虽然之前看过, 但是忘得差不多了, 属于是回炉重造了.

[用RUST进行系统编程的自学资源](https://github.com/rcore-os/rCore/wiki/study-resource-of-system-programming-in-RUST) 提供了多条高效学习Rust的路线, 结合自身情况, 决定选择 方案二 进行学习, 辅助教材使用 [Programming Rust 2nd](https://www.oreilly.com/library/view/programming-rust-2nd/9781492052586/).

## 20220629 Wed

### Rust语言圣经

学习Rust语言圣经, 有一些章节需要重点看看:

#### [1.6 避免从入门到放弃](https://course.rs/first-try/sth-you-should-not-do.html)

#### [2.2.3 语句和表达式](https://course.rs/basic/base-type/statement-expression.html)

#### [2.4.5 数组](https://course.rs/basic/compound-type/array.html)

数组切片 slice 的类型是&[i32]，与之对比，数组的类型是[i32;5]. 切片类型[T]拥有不固定的大小，而切片引用类型&[T]则具有固定的大小，因为 Rust 很多时候都需要固定大小数据类型，因此&[T]更有用.

函数调用是表达式. 表达式末尾加分号就变成了语句. 表达式如果不返回任何值, 会隐式返回一个`()`.

#### [2.6.4 全模式列表](https://course.rs/basic/match-pattern/all-patterns.html)

把这些模式语法都罗列出来，方便检索查阅.

#### [2.7. 方法](https://course.rs/basic/method.html)

impl 块中可以定义方法, 关联函数.

self, &self 和 &mut self 遵循 Rust 所有权规则.

执行方法调用时, Rust 会自动为 object 做引用和解引用.
