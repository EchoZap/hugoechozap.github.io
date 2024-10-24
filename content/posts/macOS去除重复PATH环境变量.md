---
title: "macOS去除重复PATH环境变量"
date: 2024-10-22T13:24:21+08:00
categories: ['Macos']
author: "Ronan"
---

> 环境变量分为系统和用户，这里主要配置用户环境

在 macOS 上，全局的系统环境变量文件通常可以通过以下文件和目录来配置：

1. **`/etc/profile`**：

- 这是系统范围的 shell 配置文件，适用于所有用户。当登录时，bash、sh 等 shell 会读取并执行这个文件。你可以在这里添加系统范围的环境变量。

2. **`/etc/paths`**：

- 这个文件包含系统范围的 `PATH` 环境变量。每一行是一个目录路径，文件中的所有路径会被添加到每个用户的 `PATH` 中。

3. **`/etc/paths.d/`**：

- 这是一个目录，你可以在这里放置单个文件，每个文件包含一个路径。目录中的所有文件会被自动加载，并将这些路径添加到 `PATH` 变量中。

4. **`/etc/zshenv`**（适用于 zsh）：

- 如果你使用 `zsh` 作为默认 shell（这是 macOS 默认的 shell 从 Catalina 版本开始），`/etc/zshenv` 会在每次 `zsh` 启动时被读取，是设置全局环境变量的一个好地方。

5. **`~/.zshrc` 或 `~/.bashrc`（用户级）**：

- 虽然这些是用户级配置文件，但它们通常用于设置特定用户的环境变量，而不是全局变量。如果你需要为特定用户设置变量，这些文件是最常用的地方。

# 列出当前所有PATH路径

一般使用：

```shell
echo $PATH
```

但是，这是人看的东西吗？所以，为了能让人看懂，一般使用下面这个命令：

```shell
echo $PATH | tr ':' '\n'
```

这将按行列出 PATH 中的每个路径，让你更容易查看是否有重复路径。

# 去除重复PAHT路径

### 先来一个快速但是 `治标不治本` 的方法

在 shell 配置文件中添加以下代码来自动去重 PATH 变量：

```shell
export PATH=$(echo "$PATH" | tr ':' '\n' | awk '!seen[$0]++' | tr '\n' ':' | sed 's/:$//')
```

将这段代码添加到你的 `~/.zshrc` 或 `~/.bashrc` 中的 `最后` ，然后运行 source ~/.zshrc 或 source ~/.bashrc 使其生效。

### 再来一个 `治标且治本` 的方法

对于追求简洁的本处女座，实在是受不了那东一个西一个的PATH了。众所周知，在修改 `.zshrc` 文件时，一般来说，将 `export PATH="..."` 语句放在文件的 **底部** 是更好的做法。这样做的原因如下：

1. **优先级控制**：`.zshrc` 文件中路径的顺序决定了优先级。将路径添加到底部意味着它会在其他路径之后被添加到 `PATH` 中，这样如果上面已经有其他路径添加相同的命令，比如 `python3`，你的新路径会覆盖它们，从而确保你使用的是你希望的版本。
2. **避免覆盖**：如果你在顶部添加路径，而 `.zshrc` 文件中有其他脚本或工具自动在 `PATH` 中添加路径，这些路径可能会覆盖你手动添加的路径。放在底部可以确保你的修改不会被覆盖。
3. **逻辑顺序**：一般配置文件按从上到下的顺序执行，把自定义配置放在文件的底部，可以确保所有默认配置加载完毕后，再应用你自己的配置。

所以，知道怎么做了吧，你在.zshrc里面搞个小分区(用注释分割一下)不就完了吗，简简单单：

```shell
# <<<新增加的PATH要添加在底部哦，而且一般$PATH放在要添加的路径后面，因为文件是从上往下执行的>>>
# 一些tools
export PATH=/opt:$PATH

# home-brew
export PATH="/opt/homebrew/bin/:$PATH"

#sdcc的51单片机头文件路径
export PATH="/opt/homebrew/Cellar/sdcc/4.4.0/share/sdcc/include/mcs51/:$PATH"

# 系统自带python的pip安装的程序目录
export PATH="/Users/iaa/Library/Python/3.9/lib/python/site-packages:$PATH"

# arm-none-eabi-gcc用于stm32编译工具链
export PATH="/opt/arm-none-eabi/bin:$PATH"
```
