# 环境搭建

假设大家的系统是 Linux 操作系统, 这里的教程相比 [Rust 语言圣经](https://course.rs/first-try/intro.html)多出了配置 `clippy` 的地方, 因为 `clippy` 真的非常强大好用.

## 安装 rustup

[Install Rust](https://www.rust-lang.org/tools/install) 在最显眼的位置放了这行命令

```shell
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
只需这样即可安装, 注意 rustup 不是由系统包管理器管理的, 而是装在当前用户的家目录下.

一般, 我们非 sudo 安装, 这样在服务器的多用户环境上, 不同用户可以有不同的 rustup 版本.

## 配置 crates.io 镜像

首先大家会问什么是 [crates.io](https://crates.io/)?

crates.io 是 Rust 用来托管所有库的地方, 开发过程中, 假如你需要一个输出日志的库, 需要读取命令行的库, 都可以在 crates.io 上搜索.

此外, Cargo 包管理器会管理本地的项目, 并且你在这样添加依赖的时候, Cargo 会自动去 crates.io 上下载对应的库.

```toml
[dependencies]
log = "0.4.21"
```

但由于中国恶劣的网络环境, 我们最好还是配置一个 crates.io 镜像, 这里我们按照[北外镜像](https://mirrors.bfsu.edu.cn/help/crates.io-index/)配置, 下面的指示跟文档上的是一样的, 但你可以亲自看一眼北外镜像上的文档.

Cargo 的配置文件位置一般是你自己的家目录下面的 `.cargo/config.toml`.

如果文件不存在, 创建就好了, 但应该是一个空文件.

在里面写入如下内容

```toml
[source.crates-io]
replace-with = 'mirror'

[source.mirror]
registry = "sparse+https://mirrors.bfsu.edu.cn/crates.io-index/"
```
配置好镜像后, 下载的依赖会放在你家目录下某个从属于该镜像的文件夹, 我的例子是 `/home/yangzheh/.cargo/registry/cache/mirrors4.bfsu.edu.cn-069cd02ee2d2f894`, 所以平时没有必要随便换镜像, 因为重建缓存需要再次下载.

## 安装 VScode 插件 Rust analyzer

Rust analyzer 是此类插件中的佼佼者, 它的提示和高亮是我们编程的最佳助手, 搜索和安装即可, 注意安装到 WSL 里面.

## 启用 clippy

在安装 Rust analyzer 之后, 我们配置 Rust analyzer, 使用 clippy 命令代替 check. 这会让所有编译检查都使用 clippy.

打开 VScode 设置 (JSON), 并且在其中添加这样一项:

```json
{
    // 其他设置
    "rust-analyzer.check.command": "clippy",
}
```

这样我们就用 `cargo clippy` 命令代替了默认的 `cargo check`.

为什么要用 clippy 代替 check 呢? 因为 clippy 作为一个静态 linter 启用了众多额外的检查, 可以帮助我们写出质量更好的代码, 而 check 仅仅等效于编译检查.
