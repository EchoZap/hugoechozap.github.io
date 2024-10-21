---
title: "sdcc编译与链接"
date: 2024-10-21T21:56:21.298907
categories: ['Docs']
author: "Ronan"
---
## 1.编译源文件

首先，你需要编译你的源文件（例如 `main.c`）以生成目标文件（.rel）。你可以使用以下命令：

```zsh
sdcc -c main.c
```

这条命令会生成 `main.rel` 文件。

如果你的库文件不在当前目录下并且提示头文件未找到，你可以通过 `-I` 选项指定包含文件的路径,然后使用绝对路径指定库，例如：

```sh
sdcc  -I/path/to/include -c /path/to/src/main.c 
```

这条命令也会生成 `main.rel` 文件。

如果不希望生成的目录挤在和main.c相同目录下，可以通过`-o`参数指定

```zsh
sdcc -c main.c -o /path/to/build
```

---

## 2.编译其他库文件

假设你有其他的库文件（例如 `library.c`），你需要同样地将它们编译成目标文件：

```sh
sdcc -c library.c
```

这条命令会生成 `library.rel` 文件。

你需要编译的库文件是以 `.lib` 或者 `.a` 结尾的，你可以在编译命令中指定这些库文件。

如果你的库文件不在当前目录下并且提示头文件未找到，你可以通过 `-I` 选项指定包含文件的路径,然后使用绝对路径指定库，例如：

```sh
sdcc  -I/path/to/include -c /path/to/lib/library.c
```

这条命令也会生成 `library.rel` 文件。

如果不希望生成的目录挤在和main.c相同目录下，可以通过`-o`参数指定

```zsh
sdcc -c library.c -o /path/to/build
```

---

## 3.链接所有目标文件

一旦你有了所有的目标文件，你就可以将它们链接起来生成最终的二进制文件。你可以使用以下命令：

```sh
sdcc main.rel library.rel
```

这条命令会生成一个可执行文件（例如 `a.out` 或者具体取决于你编译的目标平台）。

例如，生成`.hex`文件：

```zsh
sdcc -o out.hex main.rel library.rel
```

---

## 综合实例

假设你的项目结构如下：

```
project/
│
├── include/
│   └── library.h
│
├── src/
│   ├── main.c
│
├── lib/
│	└── library.c
│
└── Makefile
```

你可以通过以下命令编译和链接整个项目：

```sh
cd project/src
sdcc -I../include -c main.c
sdcc -I../include -c ../lib/library.c
sdcc -o out.hex main.rel library.rel
```