---
title: "个人自用 stm32 工程模板程序"
date: 2024-10-21T21:56:13.538475
categories: ['Docs']
author: "Ronan"
---
注意文件位置：`/Users/iaa/bin/STM32F4xx_DSP_StdPeriph_Lib_V1.9.0`

```shell
#!/usr/bin/env bash

# 检查是否提供了工程名参数
if [ -z "$1" ]; then
    echo "使用方法: $0 <工程名>"
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
for dir in src lib include cmsis; do
    if [ ! -d "$project_name/$dir" ]; then
        mkdir "$project_name/$dir"
    else
        echo "子目录已存在: $project_name/$dir"
    fi
done

#cmsis
cp -r /Users/iaa/bin/STM32F4xx_DSP_StdPeriph_Lib_V1.9.0/Libraries/CMSIS/Include ${project_name}/cmsis/
cp -r /Users/iaa/bin/STM32F4xx_DSP_StdPeriph_Lib_V1.9.0/Libraries/CMSIS/Device/ST/STM32F4xx/Source/Templates/TrueSTUDIO ${project_name}/cmsis/

#lib
cp -r /Users/iaa/bin/STM32F4xx_DSP_StdPeriph_Lib_V1.9.0/Libraries/STM32F4xx_StdPeriph_Driver/inc ${project_name}/lib/
cp -r /Users/iaa/bin/STM32F4xx_DSP_StdPeriph_Lib_V1.9.0/Libraries/STM32F4xx_StdPeriph_Driver/src ${project_name}/lib/

#include
cp /Users/iaa/bin/STM32F4xx_DSP_StdPeriph_Lib_V1.9.0/Libraries/CMSIS/Device/ST/STM32F4xx/Include/stm32f4xx.h ${project_name}/include/
cp /Users/iaa/bin/STM32F4xx_DSP_StdPeriph_Lib_V1.9.0/Libraries/CMSIS/Device/ST/STM32F4xx/Include/system_stm32f4xx.h ${project_name}/include/
cp /Users/iaa/bin/STM32F4xx_DSP_StdPeriph_Lib_V1.9.0/Project/STM32F4xx_StdPeriph_Templates/stm32f4xx_conf.h ${project_name}/include/
cp /Users/iaa/bin/STM32F4xx_DSP_StdPeriph_Lib_V1.9.0/Project/STM32F4xx_StdPeriph_Templates/stm32f4xx_it.h ${project_name}/include/

#src
cp /Users/iaa/bin/STM32F4xx_DSP_StdPeriph_Lib_V1.9.0/Project/STM32F4xx_StdPeriph_Templates/stm32f4xx_it.c ${project_name}/src/
cp /Users/iaa/bin/STM32F4xx_DSP_StdPeriph_Lib_V1.9.0/Project/STM32F4xx_StdPeriph_Templates/system_stm32f4xx.c ${project_name}/src/

cp /Users/iaa/bin/STM32F4xx_DSP_StdPeriph_Lib_V1.9.0/Project/STM32F4xx_StdPeriph_Templates/TrueSTUDIO/STM32F40_41xxx/STM32F417IG_FLASH.ld ${project_name}
mv ${project_name}/STM32F417IG_FLASH.ld ${project_name}/stm32_flash.ld


# 创建Makefile文件
if [ ! -e "${project_name}/Makefile" ]; then
    mkdir -p "${project_name}"
    cat > "${project_name}/Makefile" << EOF
CROSS_COMPILE = arm-none-eabi-

# 将源文件放在这里 (*.c)
SRCDIR=./src
LIBDIR=./lib/src

SRC = \$(wildcard \${LIBDIR}/*.c) \\
      \$(wildcard \${SRCDIR}/*.c)

# 将以该名称生成二进制文件 (.elf, .bin, .hex)
PROJECT_NAME=out

# 编译器设置。仅编辑 CFLAGS 以包含其他头文件。
CC = \$(CROSS_COMPILE)gcc
AS = \$(CROSS_COMPILE)as
LD = \$(CROSS_COMPILE)gcc
OBJCOPY = \$(CROSS_COMPILE)objcopy

# compiler flags 编译器标志
CFLAGS = -g -O2 -Wall -Tstm32_flash.ld
CFLAGS += -DUSE_STDPERIPH_DRIVER
CFLAGS += -D STM32F40_41xxx
CFLAGS += -mlittle-endian -mthumb -mcpu=cortex-m4 -mthumb-interwork
CFLAGS += -mfloat-abi=hard -mfpu=fpv4-sp-d16
CFLAGS += -I .
CFLAGS += -specs=nosys.specs

# 包含 STM 库中的文件
CFLAGS += -I./cmsis/Include
CFLAGS += -I./include
CFLAGS += -I./lib/inc

SRC += ./cmsis/TrueSTUDIO/startup_stm32f40_41xxx.s

# 将 SRC 中每个 .c 文件的扩展名替换为 .o，生成对应的目标文件。
OBJS = \$(SRC:.c=.o)

vpath %.c ./lib/src
vpath %.c ./src

.PHONY: proj

all: proj

proj: \$(PROJECT_NAME).elf

\$(PROJECT_NAME).elf: \$(SRC)
	\$(CC) \$(CFLAGS) \$^ -o \$@
	\$(OBJCOPY) -O ihex \$(PROJECT_NAME).elf \$(PROJECT_NAME).hex
	\$(OBJCOPY) -O binary \$(PROJECT_NAME).elf \$(PROJECT_NAME).bin

clean:
	rm -f *.o \$(PROJECT_NAME).elf \$(PROJECT_NAME).hex \$(PROJECT_NAME).bin

flash: proj
	STM32_Programmer_CLI -c port=SWD -w \$(PROJECT_NAME).hex
EOF

    echo "Makefile 已生成在: ${project_name}/Makefile"
else
    echo "Makefile 已存在: ${project_name}/Makefile"
fi


# 创建 main.c
if [ ! -e "${project_name}/src/main.c" ];then
    cat > "${project_name}/src/main.c" << EOF
#include<stm32f4xx.h>

int main(void)
{
    while(1);
}
EOF
    echo "main.c 已生成在：${project_name}/src/main.c"
else
    echo "main.c 已存在"
fi


# 创建
if [ ! -e "${project_name}/src/syscalls.c" ];then
    cat > "${project_name}/src/syscalls.c" << EOF
#include <sys/types.h>  // 包含必要的类型定义

int _close(int file) {
    return -1;
}

int _lseek(int file, int ptr, int dir) {
    return -1;
}

int _read(int file, char *ptr, int len) {
    return 0;
}

int _write(int file, char *ptr, int len) {
    return len;
}

caddr_t _sbrk(int incr) {
    extern char _end;  // 链接器脚本中定义的堆的起始位置
    static char *heap_end;
    char *prev_heap_end;

    if (heap_end == 0) {
        heap_end = &_end;
    }

    prev_heap_end = heap_end;
    heap_end += incr;

    return (caddr_t)prev_heap_end;
}
EOF
fi

rm -f ${project_name}/lib/src/stm32f4xx_fmc.c

# 注释掉 stm32f4xx_it.c 中的 #include "main.h" 和 TimingDelay_Decrement();
sed -i '' 's|#include "main.h"|//&|' "${project_name}/src/stm32f4xx_it.c"
sed -i '' 's|TimingDelay_Decrement();|//&|' "${project_name}/src/stm32f4xx_it.c"

echo "${project_name}创建成功"
```
