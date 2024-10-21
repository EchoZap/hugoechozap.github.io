---
title: "「python」打包程序"
date: 2024-10-21T21:56:43.156027
categories: ['Docs']
author: "Ronan"
---
# 通过pyinstaller打包

要将Python脚本打包成一个独立运行的应用程序，可以使用工具PyInstaller，其可以将Python脚本打包成一个可执行文件 (.exe) 。这样生成的应用程序不依赖于系统上的任何库，可以在没有Python环境的计算机上运行。一般使用pyinstaller将py文件打包成可执行文件。

> [!warning]
>
> **通过该方法打包的程序不具备跨平台性** ：如果你需要在其他操作系统上运行该程序（例如在Windows上开发并希望在macOS或Linux上运行），你需要在目标平台上执行上述步骤。PyInstaller 无法直接生成跨平台的可执行文件。

### 1. 安装 PyInstaller

首先，确保你已经安装了 PyInstaller。如果没有，可以使用以下命令进行安装：

```python
pip3 install pyinstaller
```

### 2. 打包 Python 脚本

在终端或命令提示符下，导航到包含你的 Python 脚本的目录，并运行以下命令：

```python
pyinstaller --onefile script_name.py
```

将 `script_name.py` 替换为你要打包的脚本文件名。`--onefile` 参数将所有的依赖和脚本打包成一个单一的可执行文件。

### 3. 查找生成的可执行文件

打包完成后，PyInstaller 会在当前目录下生成一个 `dist` 文件夹，其中包含你的可执行文件。你可以将这个文件复制到其他没有 Python 环境的系统上运行。

### 4. 处理依赖项

如果你的脚本有特定的依赖项（如额外的Python库），PyInstaller 会自动检测并打包它们。但如果有某些依赖项没有正确处理，你可能需要使用 `--hidden-import` 参数手动指定。

```python
pyinstaller --onefile --hidden-import=<module_name> script_name.py
```
