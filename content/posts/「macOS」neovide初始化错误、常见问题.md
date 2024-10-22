---
title: "「macOS」neovide初始化错误、常见问题"
date: 2024-10-22T13:28:44+08:00
categories: ['Docs']
author: "Ronan"
---
在 M1 macOS 中下载 neovide 之后打开遇到：

![error](https://imgs.ronan.us.kg/neovide_error.png)

#### 原因分析

由于现在所有的 macOS 版本内置终端都是默认使用 zsh ，但是 Neovide 不会在交互式 shell 中启动嵌入式 neovim 实例，因此 shell 不会读取其启动文件的一部分（ `~/.bashrc` `~/.zshrc` 无论你的 shell 的等效项是什么）。

#### 解决方法

1.首先找到当前安装的 neovim 路径，通过 `which nvim` 查看，例如

```zsh
❯ which nvim

/opt/homebrew/bin/nvim
```

`/opt/homebrew/bin/nvim` 就是 nvim 的路径，记住它！！！


2.根据自己的 shell，例如：

- 对于 zsh，将 `export PATH="/opt/homebrew/bin:$PATH"`  放入 `~/.zprofile` 或 `~/.zlogin` 中。
- 对于 bash，将 `export PATH="/opt/homebrew/bin:$PATH"`  放入 `~/.profile` 。
