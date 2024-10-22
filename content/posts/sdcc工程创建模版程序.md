---
title: "sdcc工程创建模版程序"
date: 2024-10-22T13:23:57+08:00
categories: ['Tools']
author: "Ronan"
---
## 1.程序构建

新建一个txt文件并将以下代码复制到`xxx.txt`，之后将`xxx.txt（例如sdccpj.txt）`后缀名修改为`.sh（例如sdccpj.sh）`或者直接去掉后缀名只保留文件名（这在类linux系统中就是可执行程序）`sdccpj`。

```shell
#!/usr/bin/env bash

# 检查是否提供了工程名参数
if [ -z "$1" ]; then
    echo "使用方法: sdccpj <工程名>"
    exit 1
fi

# 获取工程名
project_name=$1

# 创建工程根目录
if [ ! -d "$project_name" ]; then
    mkdir "$project_name"
else
    echo "根目录已存在: $project_name"
fi

# 创建子目录
for dir in src lib include build; do
    if [ ! -d "$project_name/$dir" ]; then
        mkdir "$project_name/$dir"
    else
        echo "子目录已存在: $project_name/$dir"
    fi
done

# 创建Makefile文件
if [ ! -e "${project_name}/Makefile" ]; then
    cat > ${project_name}/Makefile << EOF
# 定义编译器
CC = sdcc

# 定义源文件目录
SRC_DIR = src
LIB_DIR = lib

# 定义头文件目录
INCLUDE_DIR = include

# 定义生成文件目录
BUILD_DIR = build

# 定义所有源文件
SRC_FILES = \$(wildcard \$(SRC_DIR)/*.c)
LIB_FILES = \$(wildcard \$(LIB_DIR)/*.c)

# 定义目标文件
OBJ_FILES = \$(patsubst \$(SRC_DIR)/%.c, \$(BUILD_DIR)/%.rel, \$(SRC_FILES)) \\
            \$(patsubst \$(LIB_DIR)/%.c, \$(BUILD_DIR)/%.rel, \$(LIB_FILES))

# 定义最终生成的hex文件
OUTPUT_FILE = \$(BUILD_DIR)/out.hex

# 查找串口号
PORT = \$(wildcard (ls /dev/tty.wchusbserial* 2>/dev/null | head -n 1))

# 默认目标
all: \$(OUTPUT_FILE) post_build_cleanup

# 编译每一个.c文件生成.rel文件
\$(BUILD_DIR)/%.rel: \$(SRC_DIR)/%.c
	\$(CC) -I\$(INCLUDE_DIR) -c \$< -o \$@

\$(BUILD_DIR)/%.rel: \$(LIB_DIR)/%.c
	\$(CC) -I\$(INCLUDE_DIR) -c \$< -o \$@

# 链接所有.rel文件生成.hex文件
\$(OUTPUT_FILE): \$(OBJ_FILES)
	\$(CC) \$(OBJ_FILES) -o \$(OUTPUT_FILE)

# 定义一个伪目标，用于清理编译后生成的文件
.PHONY: post_build_cleanup
post_build_cleanup:
	-rm -f \$(BUILD_DIR)/*.asm \$(BUILD_DIR)/*.lst \$(BUILD_DIR)/*.rst \$(BUILD_DIR)/*.sym \$(BUILD_DIR)/*.lk \\
	\$(BUILD_DIR)/*.map \$(BUILD_DIR)/*.mem 
    @echo "构建成功，泰裤辣！可烧录文件out.hex已存放到build目录下"

# 定义一个clean目标，用于手动清理所有生成的文件
.PHONY: clean
clean:
	-rm -f \$(BUILD_DIR)/*.rel \$(BUILD_DIR)/*.hex
	-rm -f \$(BUILD_DIR)/*.asm \$(BUILD_DIR)/*.lst \$(BUILD_DIR)/*.rst \$(BUILD_DIR)/*.sym \$(BUILD_DIR)/*.lk \\
	\$(BUILD_DIR)/*.map \$(BUILD_DIR)/*.mem
	@echo "已成功清空了所有的生成文件..."

# 定义一个flash目标，用于手动烧录.hex文件
.PHONY: flash
flash:
	stcgal -p \$(PORT) -b 9600 \$(BUILD_DIR)/out.hex
EOF
else
    echo "Makefile 已存在: $project_name/Makefile"
fi
```


## 2.创建工程

通过在终端键入命令(以上述sdccpj为例)

```shell
sdccpj <所需工程名>
```

这将在当前工作目录下创建以下层级目录，可在src存放程序主代码。通过`make`即可构建整个工程，构建生成文件在build目录下，通过`make clean`可清空build目录生成的文件

```shell
test
├── Makefile
├── build
├── include
├── lib
└── src
```