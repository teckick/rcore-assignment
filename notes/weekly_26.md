# Weekly 26

计划用第1周和第2周完整学习 Rust 的核心部分, 虽然之前看过, 但是忘得差不多了, 属于是回炉重造了.

[用RUST进行系统编程的自学资源](https://github.com/rcore-os/rCore/wiki/study-resource-of-system-programming-in-RUST) 提供了多条高效学习Rust的路线, 结合自身情况, 决定选择 方案二 进行学习, 辅助教材使用 [Programming Rust 2nd](https://www.oreilly.com/library/view/programming-rust-2nd/9781492052586/).

## 20220629 Wed

### Rust语言圣经

今天学习了Rust语言圣经章节 1.1 ~ 2.7, 有一些章节需要重点看看:

#### [1.6 避免从入门到放弃](https://course.rs/first-try/sth-you-should-not-do.html)

#### [2.2.3 语句和表达式](https://course.rs/basic/base-type/statement-expression.html)

#### [2.4.5 数组](https://course.rs/basic/compound-type/array.html)

数组切片 slice 的类型是&[i32]，与之对比，数组的类型是[i32;5]. 切片类型[T]拥有不固定的大小，而切片引用类型&[T]则具有固定的大小，因为 Rust 很多时候都需要固定大小数据类型，因此&[T]更有用.

函数调用是表达式. 表达式末尾加分号就变成了语句. 表达式如果不返回任何值, 会隐式返回一个`()`.

#### [2.6.4 全模式列表](https://course.rs/basic/match-pattern/all-patterns.html)

把这些模式语法都罗列出来，方便检索查阅.

#### [2.7 方法](https://course.rs/basic/method.html)

impl 块中可以定义方法, 关联函数.

self, &self 和 &mut self 遵循 Rust 所有权规则.

执行方法调用时, Rust 会自动为 object 做引用和解引用.

## 20220630 Thu

### Rust语言圣经

今天学习了Rust语言圣经章节 2.8 ~ 2.11, 有一些章节需要重点看看:

#### [2.8.1 泛型](https://course.rs/basic/trait/generic.html)

Rust 1.51 引入了 const 泛型. 简单来说, 传统泛型是针对类型的泛型, 而 const 泛型是针对值的泛型.

```rust
fn display_array<T: std::fmt::Debug, const N: usize>(arr: [T; N]) {
    println!("{:?}", arr);
}
```

这里的T是普通泛型, N就是 const 泛型, 定义的语法是 const N: usize, 表示 const 泛型 N, 它基于的值类型是 usize.

#### [2.8.3 特征对象](https://course.rs/basic/trait/trait-object.html)

Box<dyn Draw> 和 &dyn Draw 的主要区别在于创特征对象的方式不同. TODO: 实现上的不同?

dyn 不能单独作为特征对象的定义, 因为特征对象可以是任意实现了某个特征的类型，编译器在编译期不知道该类型的大小. 而 &dyn 和 Box<dyn> 在编译期都是已知大小，所以可以用作特征对象的定义.

特征对象是动态分发的.

**[特征对象的限制](https://course.rs/basic/trait/trait-object.html#%E7%89%B9%E5%BE%81%E5%AF%B9%E8%B1%A1%E7%9A%84%E9%99%90%E5%88%B6)**

对象安全:

不是所有特征都能拥有特征对象，只有对象安全的特征才行。当一个特征的所有方法都有如下属性时，它的对象才是安全的：

- 方法的返回类型不能是 Self
- 方法没有任何泛型参数

#### [2.8.4 进一步深入特征](https://course.rs/basic/trait/advance-trait.html)

关联类型和泛型在功能上无本质区别, 主要作用是提升代码可读性. 使用泛型，导致函数头部也必须增加泛型的声明，而使用关联类型，将得到可读性好得多的代码.

完全限定语法用于解决同名函数调用冲突问题:

```rust
<Type as Trait>::function(receiver_if_method, next_arg, ...);
```

可以使用 **newtype 模式**, 在外部类型上实现外部特征.

```rust
use std::fmt;

struct Wrapper(Vec<String>);

impl fmt::Display for Wrapper {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "[{}]", self.0.join(", "))
    }
}
```

#### [2.10 类型转换](https://course.rs/basic/converse.html)

点操作符的类型转换:

- 尝试值方法调用: T::foo(value)
- 尝试引用方法调用: <&T>::foo(value) 和 <&mut T>::foo(value)
- 尝试解引用方法调用: T: Deref<Target = U> U::foo(value)
- 尝试不定长转换后方法调用: [i32; 2] 转为 [i32]
- 编译报错

通过例子多练习一下. 教程给的例子还挺好的, Rust确实为提升人机工程学做了努力:

以下例子, 需要对array执行索引操作, 需要array实现Index trait, 经过一系列的自动类型转换, Rc<Box<[T; 3]>> -> Box<[T; 3]> -> [T; 3] -> [T] 最终成功执行调用.

```rust
let array: Rc<Box<[T; 3]>> = ...;
let first_entry = array[0];
```

## 20220701 Fri

### Rust语言圣经

继续学习Rust语言圣经, 章节 2.12 ~ 2.14, 有一些章节需要重点看看:

[2.12 包和模块](https://course.rs/basic/crate-module/intro.html)

Rust 包管理中的4个层级的概念: Package, Workspace, Crate, Module.

Module 注意父子模块的可见性. 限制可见性语法, 细粒度控制包的可见性.

推荐使用 `lib.rs` (推荐) 和 `crates.io` 查找三方包.
