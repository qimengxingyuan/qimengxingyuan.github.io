---
title: Rust 开发环境搭建
top: false
cover: false
toc: true
mathjax: true
abbrlink: 44351
date: 2022-06-19 16:50:49
password:
summary:
tags:
	- Rust
categories:
	- 编程语言
---

# rust吉祥物
Ferris 是 Rust 社区的非官方吉祥物。

![](http://image.yhzhao.cn/img20220619165804.png)

# 开发环境的安装
一般大家都是使用 [rustup](https://rustup.rs/) 进行 Rust 工具链的安装和管理，只需要根据官方网站上的说明先安装 rustup 即可~

安装脚本：

```
# 可以先执行这两行，可以加速下载
$ export RUSTUP_DIST_SERVER="https://rsproxy.cn"
$ export RUSTUP_UPDATE_ROOT="https://rsproxy.cn/rustup"

# 或者把这两行加入到你的 ~/.zshrc 或者 ~/.profile 文件中，并 source 一次
$ echo 'export RUSTUP_DIST_SERVER="https://rsproxy.cn"' >> ~/.zshrc
$ echo 'export RUSTUP_UPDATE_ROOT="https://rsproxy.cn/rustup"' >> ~/.zshrc
$ source ~/.zshrc

# 执行安装命令
$ curl --proto '=https' --tlsv1.2 -sSf https://rsproxy.cn/rustup-init.sh | sh
```

一般不需要额外选择安装的策略，按照默认安装即可。
安装完成后，需要配置一下 cargo 使用的源：

```
$ mkdir -p ~/.cargo
$ vim ~/.cargo/config
```

输入以下内容并[保存退出](https://stackoverflow.com/questions/11828270/how-do-i-exit-the-vim-editor)：

```
# 加速下载
[source.crates-io]
replace-with = 'rsproxy'

[source.rsproxy]
registry = "https://rsproxy.cn/crates.io-index"

[registries.rsproxy]
index = "https://rsproxy.cn/crates.io-index"

[net]
git-fetch-with-cli = true
```

# IDE
IDE推荐使用Clion，个人感觉用起来比较顺手
可以在插件市场里面查找`Rust`
