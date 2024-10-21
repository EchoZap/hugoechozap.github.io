---
title: "「各种摸不着头脑」意外修改PATH(环境变量)"
date: 2024-10-21T21:54:24.870021
categories: ['Linux']
author: "Ronan"
---
# 1. **检查sudo是否已经安装**

确保 **sudo** 已经正确地安装在你的系统中。你可以通过运行 `which sudo` 来检查它的路径。这应该会出现以下内容
```shell
❯ which sudo
/usr/bin/sudo
```
如果它没有返回任何内容，那么你需要安装 **sudo**。在大多数 Linux 发行版上，你可以使用包管理器来安装 **sudo**。例如，在 Ubuntu 上，你可以使用以下命令安装：
```shell
sudo apt-get install sudo
```
在其他发行版上，你可能需要使用不同的命令来安装 **sudo**。

# 2.**手动指定sudo命令的路径**

如果你知道 **sudo** 命令的确切路径，你可以在使用时直接指定它的路径。例如：
```shell
/usr/bin/sudo /usr/bin/vim .bashrc
```

**修复PATH环境变量**：更彻底的解决方法是修复 **PATH** 环境变量，使得系统能够找到 **sudo** 命令。你可以编辑你的  **.bashrc** 文件，将正确的路径添加到 **PATH** 环境变量中。你可以使用下面的命令来编辑  **.bashrc** 文件：
```shell
export PATH=$PATH:/usr/bin
```
然后保存文件并重新启动终端，或者运行 **source ~/.bashrc** 来使修改生效。

# 3. **重启系统(针对一些Linux系统，macOS通常不需要)**：

在修改了环境变量之后，可能需要重启系统才能使其生效。
