# 逻辑控制流和异常处理

## 程序控制流

对于 C/C++ 来讲, 我们知道有 `if-else`, `for`, `while`. 偶尔我们可能还会想到 `switch`.

对于 Rust 而言, 也有直接的对应

```rust
fn is_2(num: i32) -> bool {
    num == 2
}

fn main() {
    let x = 3;
    // if-else
    if is_2(x) {
        println!("x is 2");
    } else if x > 2 {
        println!("x > 2");
    } else {
        println!("x < 2");
    }
    // for-loop
    for i in 0..10 {
        // let i statement can be omitted
        println!("i = {}", i);
    }
    let mut i = 100;
    // while-loop
    while !is_2(i) {
        println!("{}", i);
        i -= 1;
    }
}
```

可能同学会说, `for i in 0..10` 这里没有写成 `let i`, 怎么还 OK 呢? 原因是这是 Rust 的语法糖, 简化了我们的写法.

## 异常处理

但如果 Rust 的程序控制流只有这样, 那就太简单了, 我们还需要异常处理.

对于 C/C++ 而言, 异常处理并不是内置功能, 典型的 C/C++ 里面, 经常能看到这种写法

```cpp
#include <iostream>
#include <string>

#define OK        200
#define NOT_FOUND 404
// ...
    
void print_html(std::string content, int code)
{
    if (code == OK)
        std::cout << content;
    else if (code == NOT_FOUND)
        std::cout << "404" << std::endl;
    // ...
}
```

即用一个 `int` 指示状态, 后面对每种进行判断.

再比如, 有时候用空指针表示没有找到数据, 数据为空等等.

```cpp
/// select a person from the database
Person *p get_person()
{
    // do something
    if (can connect to database)
    {
        // do something
        p = balabala;
        return p;
    }
    else
        return nullptr;
}
```

在 Rust 中, 有两种特殊的泛型类型 `Option` 和 `Result` 被用于上述两种使用场景.

所谓泛型, 具体说 `Option<T>` 代表值可以为 `Some(T)` 或者 `None` 两种情况, `Result<T, E>` 代表值可以为 `Ok(T)` 或者 `Err(E)` 两种情况.

```rust
fn get_html() -> Result<String, u16> {
    Err(400)
}

fn main() {
    let result = get_html();
    match result {
        Ok(html) => println!("HTML: {}", html),
        Err(status) => println!("Error: {}", status),
    }
}
```

我们再来看看 `Option` 的使用

```rust
struct Person {
    name: String,
}

fn get_person() -> Option<Person> {
    None
}

fn main() {
    let person = get_person();
    if let Some(person) = person {
        println!("Found a person: {}", person.name);
    } else {
        println!("No person found");
    }
}
```

这样写的好处在于, 与其在 C/C++ 中用指向数据的指针是否为 `nullptr` 来判断, (这会在编程中有意无意地造成非常多的错误), 我们可以直接用 `Option` 和 `Result` 来表示, 这强迫语言的使用者进行错误处理, 从而减少错误.
