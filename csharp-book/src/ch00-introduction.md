# Rust 面向 C# 程序员：完整培训指南

面向有 C# 经验开发者的全面 Rust 学习指南。本指南涵盖从基础语法到高级模式的所有内容，重点介绍两种语言之间的概念转变和实践差异。

## 课程概览
- **选择 Rust 的理由** — 为什么 Rust 对 C# 开发者很重要：性能、安全性与正确性
- **入门** — 安装、工具链与第一个程序
- **基本构建块** — 类型、变量、控制流
- **数据结构** — 数组、元组、结构体、集合
- **模式匹配与枚举** — 代数数据类型与穷举匹配
- **所有权与借用** — Rust 的内存管理模型
- **模块与 crate** — 代码组织与依赖管理
- **错误处理** — 基于 Result 的错误传播
- **Trait 与泛型** — Rust 的类型系统
- **闭包与迭代器** — 函数式编程模式
- **并发** — 借助类型系统保证实现无畏并发，async/await 深度解析
- **Unsafe Rust 与 FFI** — 何时以及如何突破安全 Rust 的边界
- **迁移模式** — 真实世界中 C# 到 Rust 的模式与渐进式采用
- **最佳实践** — 面向 C# 开发者的地道 Rust 写法

---

# 自学指南

本材料既可用于讲师主导的课程，也可用于自学。如果你正在自行学习，以下是如何充分利用本材料的建议。

**进度建议：**

| 章节 | 主题 | 建议时长 | 检查点 |
|----------|-------|---------------|------------|
| 1–4 | 环境搭建、类型、控制流 | 1 天 | 能用 Rust 编写 CLI 温度转换工具 |
| 5–6 | 数据结构、枚举、模式匹配 | 1–2 天 | 能定义带数据的枚举并对其进行穷举 `match` |
| 7 | 所有权与借用 | 1–2 天 | 能解释*为什么* `let s2 = s1` 会使 `s1` 失效 |
| 8–9 | 模块、错误处理 | 1 天 | 能创建使用 `?` 传播错误的多文件项目 |
| 10–12 | Trait、泛型、闭包、迭代器 | 1–2 天 | 能将 LINQ 链转换为 Rust 迭代器 |
| 13 | 并发与 async | 1 天 | 能用 `Arc<Mutex<T>>` 编写线程安全计数器 |
| 14 | Unsafe Rust、FFI、测试 | 1 天 | 能通过 P/Invoke 从 C# 调用 Rust 函数 |
| 15–16 | 迁移、最佳实践、工具链 | 按自己节奏 | 参考材料 — 在编写真实代码时随时查阅 |
| 17 | 综合项目 | 1–2 天 | 拥有一个可工作的、能获取天气数据的 CLI 工具 |

**如何使用练习题：**
- 各章节在可折叠的 `<details>` 块中包含带答案的动手练习
- **在展开答案之前，请始终先尝试练习。** 与借用检查器的较量是学习的一部分 — 编译器的错误信息就是你的老师
- 如果卡住超过 15 分钟，展开答案，认真研究，然后关闭它，从头再试一遍
- [Rust Playground](https://play.rust-lang.org/) 让你无需本地安装即可运行代码

**难度指示：**
- 🟢 **入门** — 直接从 C# 概念翻译过来
- 🟡 **中级** — 需要理解所有权或 trait
- 🔴 **高级** — 生命周期、async 内部机制或 unsafe 代码

**遇到瓶颈时：**
- 仔细阅读编译器错误信息 — Rust 的错误信息异常有用
- 重读相关章节；像所有权（第7章）这样的概念往往在第二遍时才豁然开朗
- [Rust 标准库文档](https://doc.rust-lang.org/std/) 非常出色 — 搜索任何类型或方法
- 更深入的 async 模式，请参见配套的 [Async Rust 培训](../async-book/)

---

# 目录

## 第一部分 — 基础

### 1. 介绍与动机 🟢
- [Rust 对 C# 开发者的价值](ch01-introduction-and-motivation.md#the-case-for-rust-for-c-developers)
- [Rust 所解决的常见 C# 痛点](ch01-introduction-and-motivation.md#common-c-pain-points-that-rust-addresses)
- [何时选择 Rust 而非 C#](ch01-introduction-and-motivation.md#when-to-choose-rust-over-c)
- [语言哲学对比](ch01-introduction-and-motivation.md#language-philosophy-comparison)
- [快速参考：Rust 与 C# 对比](ch01-introduction-and-motivation.md#quick-reference-rust-vs-c)

### 2. 入门指南 🟢
- [安装与配置](ch02-getting-started.md#installation-and-setup)
- [第一个 Rust 程序](ch02-getting-started.md#your-first-rust-program)
- [Cargo 与 NuGet/MSBuild 对比](ch02-getting-started.md#cargo-vs-nugetmsbuild)
- [读取输入与 CLI 参数](ch02-getting-started.md#reading-input-and-cli-arguments)
- [Rust 基本关键字 *(可选参考 — 按需查阅)*](ch02-1-essential-keywords-reference.md#essential-rust-keywords-for-c-developers)

### 3. 内置类型与变量 🟢
- [变量与可变性](ch03-built-in-types-and-variables.md#variables-and-mutability)
- [基本类型对比](ch03-built-in-types-and-variables.md#primitive-types)
- [字符串类型：String 与 &str](ch03-built-in-types-and-variables.md#string-types-string-vs-str)
- [打印与字符串格式化](ch03-built-in-types-and-variables.md#printing-and-string-formatting)
- [类型转换](ch03-built-in-types-and-variables.md#type-casting-and-conversions)
- [真正的不可变性与 Record 的假象](ch03-1-true-immutability-vs-record-illusions.md#true-immutability-vs-record-illusions)

### 4. 控制流 🟢
- [函数与方法](ch04-control-flow.md#functions-vs-methods)
- [表达式与语句（重要！）](ch04-control-flow.md#expression-vs-statement-important)
- [条件语句](ch04-control-flow.md#conditional-statements)
- [循环与迭代](ch04-control-flow.md#loops)

### 5. 数据结构与集合 🟢
- [元组与解构](ch05-data-structures-and-collections.md#tuples-and-destructuring)
- [数组与切片](ch05-data-structures-and-collections.md#arrays-and-slices)
- [结构体与类对比](ch05-data-structures-and-collections.md#structs-vs-classes)
- [构造器模式](ch05-1-constructor-patterns.md#constructor-patterns)
- [`Vec<T>` 与 `List<T>` 对比](ch05-2-collections-vec-hashmap-and-iterators.md#vect-vs-listt)
- [HashMap 与 Dictionary 对比](ch05-2-collections-vec-hashmap-and-iterators.md#hashmap-vs-dictionary)

### 6. 枚举与模式匹配 🟡
- [代数数据类型与 C# 联合类型对比](ch06-enums-and-pattern-matching.md#algebraic-data-types-vs-c-unions)
- [穷举模式匹配](ch06-1-exhaustive-matching-and-null-safety.md#exhaustive-pattern-matching-compiler-guarantees-vs-runtime-errors)
- [使用 `Option<T>` 保证空值安全](ch06-1-exhaustive-matching-and-null-safety.md#null-safety-nullablet-vs-optiont)
- [守卫与高级模式](ch06-enums-and-pattern-matching.md#guards-and-advanced-patterns)

### 7. 所有权与借用 🟡
- [理解所有权](ch07-ownership-and-borrowing.md#understanding-ownership)
- [移动语义与引用语义对比](ch07-ownership-and-borrowing.md#move-semantics)
- [借用与引用](ch07-ownership-and-borrowing.md#borrowing-basics)
- [内存安全深度解析](ch07-1-memory-safety-deep-dive.md#references-vs-pointers)
- [生命周期深度解析](ch07-2-lifetimes-deep-dive.md#lifetimes-telling-the-compiler-how-long-references-live) 🔴
- [智能指针、Drop 与 Deref](ch07-3-smart-pointers-beyond-single-ownership.md#smart-pointers-when-single-ownership-isnt-enough) 🔴

### 8. Crate 与模块 🟢
- [Rust 模块与 C# 命名空间对比](ch08-crates-and-modules.md#rust-modules-vs-c-namespaces)
- [Crate 与 .NET 程序集对比](ch08-crates-and-modules.md#crates-vs-net-assemblies)
- [包管理：Cargo 与 NuGet 对比](ch08-1-package-management-cargo-vs-nuget.md#package-management-cargo-vs-nuget)

### 9. 错误处理 🟡
- [异常与 `Result<T, E>` 对比](ch09-error-handling.md#exceptions-vs-resultt-e)
- [? 运算符](ch09-error-handling.md#the--operator-propagating-errors-concisely)
- [自定义错误类型](ch06-1-exhaustive-matching-and-null-safety.md#custom-error-types)
- [Crate 级别错误类型与 Result 别名](ch09-1-crate-level-error-types-and-result-alias.md#crate-level-error-types-and-result-aliases)
- [错误恢复模式](ch09-1-crate-level-error-types-and-result-alias.md#error-recovery-patterns)

### 10. Trait 与泛型 🟡
- [Trait 与接口对比](ch10-traits-and-generics.md#traits---rusts-interfaces)
- [继承与组合对比](ch10-2-inheritance-vs-composition.md#inheritance-vs-composition)
- [泛型约束：where 与 trait bounds 对比](ch10-1-generic-constraints.md#generic-constraints-where-vs-trait-bounds)
- [常用标准库 Trait](ch10-traits-and-generics.md#common-standard-library-traits)

### 11. From 与 Into Trait 🟡
- [Rust 中的类型转换](ch11-from-and-into-traits.md#type-conversions-in-rust)
- [为自定义类型实现 From](ch11-from-and-into-traits.md#rust-from-and-into)

### 12. 闭包与迭代器 🟡
- [Rust 闭包](ch12-closures-and-iterators.md#rust-closures)
- [LINQ 与 Rust 迭代器对比](ch12-closures-and-iterators.md#linq-vs-rust-iterators)
- [宏入门](ch12-1-macros-primer.md#macros-code-that-writes-code)

---

## 第二部分 — 并发与系统

### 13. 并发 🔴
- [线程安全：惯例约束与类型系统保证对比](ch13-concurrency.md#thread-safety-convention-vs-type-system-guarantees)
- [async/await：C# Task 与 Rust Future 对比](ch13-1-asyncawait-deep-dive.md#async-programming-c-task-vs-rust-future)
- [取消模式](ch13-1-asyncawait-deep-dive.md#cancellation-cancellationtoken-vs-drop--select)
- [Pin 与 tokio::spawn](ch13-1-asyncawait-deep-dive.md#pin-why-rust-async-has-a-concept-c-doesnt)

### 14. Unsafe Rust、FFI 与测试 🟡
- [何时以及为何使用 Unsafe](ch14-unsafe-rust-and-ffi.md#when-you-need-unsafe)
- [通过 FFI 与 C# 互操作](ch14-unsafe-rust-and-ffi.md#interop-with-c-via-ffi)
- [Rust 与 C# 中的测试对比](ch14-1-testing.md#testing-in-rust-vs-c)
- [属性测试与 Mock](ch14-1-testing.md#property-testing-proving-correctness-at-scale)

---

## 第三部分 — 迁移与最佳实践

### 15. 迁移模式与案例研究 🟡
- [Rust 中的常见 C# 模式](ch15-migration-patterns-and-case-studies.md#common-c-patterns-in-rust)
- [面向 C# 开发者的必备 Crate](ch15-1-essential-crates-for-c-developers.md#essential-crates-for-c-developers)
- [渐进式采用策略](ch15-2-incremental-adoption-strategy.md#incremental-adoption-strategy)

### 16. 最佳实践与参考 🟡
- [面向 C# 开发者的地道 Rust 写法](ch16-best-practices.md#best-practices-for-c-developers)
- [性能对比：托管代码与原生代码](ch16-1-performance-comparison-and-migration.md#performance-comparison-managed-vs-native)
- [常见陷阱与解决方案](ch16-2-learning-path-and-resources.md#common-pitfalls-for-c-developers)
- [学习路径与资源](ch16-2-learning-path-and-resources.md#learning-path-and-next-steps)
- [Rust 工具生态](ch16-3-rust-tooling-ecosystem.md#essential-rust-tooling-for-c-developers)

---

## 综合项目

### 17. 综合项目 🟡
- [构建 CLI 天气工具](ch17-capstone-project.md#capstone-project-build-a-cli-weather-tool) — 将结构体、trait、错误处理、async、模块、serde 与测试融合成一个可工作的应用程序
