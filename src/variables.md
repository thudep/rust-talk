# 变量: 基本类型和声明

这里假定大家熟悉 C/C++ 的写法, 我们会在这里通过比较的方式学习 Rust 的特性.

## 基本类型

### 整数

如果大家正在学习 C/C++, 你会知道 `int` 一般是 4 个字节的, `long int` 可能是 8 个字节的, 那么 `long long int` 会比 `long int` 更长吗?

下面这段 C/C++ 代码告诉我们, 这几个类型所占的大小分别是 8, 8, 4, 2.

```cpp
#include <cstdio>

int main() {
  printf("%lu %lu %lu %lu\n", sizeof(long long int), sizeof(long int),
         sizeof(int), sizeof(short int));
  printf("%lu %lu %lu %lu\n", sizeof(long long unsigned), sizeof(long unsigned),
         sizeof(unsigned), sizeof(short unsigned));
  return 0;
}
```

并且, C++ 中, `int` 到底有多大, 是由平台上的编译器决定的, 在我的机器上是 4 个字节, 在你的机器上可能是 2 个字节, 这是合法的行为.

但在 Rust 中, 我们会精确地指定类型的大小

```rust
fn main() {
    println!("Hello, World!");
    println!(
        "{} {} {} {}",
        std::mem::size_of::<i64>(),
        std::mem::size_of::<i32>(),
        std::mem::size_of::<i16>(),
        std::mem::size_of::<i8>()
    );
    // 8, 4, 2, 1
    use std::mem::size_of;
    println!(
        "{} {} {} {}",
        size_of::<u64>(),
        size_of::<u32>(),
        size_of::<u16>(),
        size_of::<u8>()
    );
}
```

可以看到, `i32` 就是 4 个字节的有符号整数, `u32` 就是 4 个字节的无符号整数.

根据 [The reference](https://doc.rust-lang.org/reference/type-layout.html?#primitive-representations) 所有跟整数有关的类型有且只有这些:

`u8`, `u16`, `u32`, `u64`, `u128`, `usize`, `i8`, `i16`, `i32`, `i64`, `i128`, `isize`

我们会问, `usize` 是什么类型? 为什么需要一个大小不定的整数类型呢?

这是因为 [`uszie`](https://doc.rust-lang.org/std/primitive.usize.html) ([中文文档](https://rustwiki.org/zh-CN/std/primitive.usize.html)) 往往出现在迭代器的索引中, 大小为机器的指针大小, 目前有 32 位和 64 位两种.

### 字符

Rust 设计之初, 就规定了所有字符 (`char`) 是 Unicode 字符. (多么平等! 给大家带来了多少便捷!)

例如以下代码

```rust
fn main() {
    let a: char = 'b';
    let i = '我';
    println!("a{}c", a);
    println!(
        "size of 'a' and '我' is {}, {}",
        std::mem::size_of_val(&a),
        std::mem::size_of_val(&i)
    );
    println!(
        "they are both char with size of {}",
        std::mem::size_of::<char>()
    );
}
```

### 其他类型

我们还会见到单位类型 (或者说空类型) `()` 和布尔类型 `bool`, 他们的大小分别是 0 和 1 个字节.

```rust
fn main() {
    println!("size of bool is {}", std::mem::size_of::<bool>());
    println!(
        "size of unit type or empty type () is {}",
        std::mem::size_of::<()>()
    );
}
```

此外, 浮点数有 `f32` 和 `f64` 两种.

对于单元类型(空类型), 我们要多讨论一下, 它是什么东西呢?

它是无返回值的函数的返回值的类型, 这句话有点绕, 我们还是和 C/C++ 对比一下

```cpp
#include <cstdio>

void do_something1()
{
    printf("This function does not return");
    return;
}

void do_something2()
{
    printf("This function does not return");
}

int return_something()
{
    return 1;
}
```

对于 Rust 而言, 这是类似的

```rust
fn do_something1() {
    println!("this function does not return anything, or it returns ()");
    return;
}

fn do_something2() -> () {
    println!("this function returns () too");
}

fn main() {
    do_something1();
    do_something2();
}
```

但是, 你会发现, Rust 工具链给了提示, 告诉我们
  - `return;` 这样的指定退出的写法是不必要的, 因为在函数末尾本来就要退出, 不必指明
  - `-> ()` 这样的类型标柱也是不必要的

最后我们接受提示的修改, 把代码写成这样, 并且在以后习惯这样写

```rust
fn do_something1() {
    println!("this function does not return anything, or it returns ()");
}

fn do_something2() {
    println!("this function returns () too");
}

fn main() {
    do_something1();
    do_something2();
}
```

## 声明

在 Rust 中, 声明一个变量使用 `let` 关键字, 注意默认声明的变量都是不可变的, 使用 `mut` (代表 mutable) 关键字可以声明可变的变量.

尽管

```rust
fn main() {
    let a: i32;
    let x = 1;
    println!("{}", 1);
    a = x;
    println!("{}", a);
    // a = 2; // 错误
    let y = x + 1;
    println!("{}", y);
    let x = y; // 变量遮盖
    println!("{}", x);
    let mut z = y; // 变量默认是不可变的, mut 关键字可以声明可变的变量
    z += 1;
    println!("{}", z);
}
```
变量遮盖的用处我们会在后面讲, 但我们这里可以说, 被遮盖的变量不能再被访问了, 可能很快会被销毁等等.

```rust
extern crate serde;
extern crate serde_json;

use serde::Deserialize;
use serde_json::Error;

#[derive(Deserialize)]
struct Point {
    x: f64,
    y: String,
}

fn main() {
    let point = r#"{"x":0.0,"y":"0.0"}"#;
    let point: Result<Point, Error> = serde_json::from_str(point);
    if let Ok(point) = point {
        println!("x: {}, y: {}", point.x, point.y);
    } else {
        println!("Error parsing JSON");
    }
}
```

注意这里需要附带 `Cargo.toml`, 以便下载 `serde` 以及 `serde_json` 两个库.

```toml
[package]
name = "demo"
version = "0.1.0"
edition = "2021"

[dependencies]
serde = { "version" = "1.0.203", features = ["derive"] }
serde_json = "1.0.120"
```