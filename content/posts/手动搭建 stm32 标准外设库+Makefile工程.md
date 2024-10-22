---
title: "手动搭建 stm32 标准外设库+Makefile工程"
date: 2024-10-22T13:29:00+08:00
categories: ['Docs']
author: "Ronan"
---
> 本教程以 **stm32f407** 为例，其他 stm32 芯片配置方式大同小异。

# 1.下载官方标准外设库

进入 [ST 官网的嵌入式软件板块](https://www.st.com.cn/zh/embedded-software/stm32-standard-peripheral-libraries.html) ，根据自身板子型号选择，有F0-F4多种型号。

之后根据提示选择下载即可，如果没有意外的话，应该会获得一个类似 **en.stsw-stm32065_v1-9-0.zip(这是 F4 的标准库)** 的压缩包。

# 2.创建工程

## 2.1建立工程结构

1.解压从官网下载的标准外设库 **en.stsw-stm32065_v1-9-0.zip** ，获得一个 **STM32F4xx_DSP_StdPeriph_Lib_V1.9.0** 目录

2.新建一个`my_project`目录，并在该目录下创建四个子目录：`driver`、`include`、`lib`、`src`

3.将标准固件库目录`.../STM32F4xx_DSP_StdPeriph_Lib_V1.9.0/Libraries/CMSIS/Include`文件夹，以及固件库文件目录`.../STM32F4xx_DSP_StdPeriph_Lib_V1.9.0/Libraries/CMSIS/Device/ST/STM32F4xx/Source/Templates/TrueSTUDIO`文件夹，全部拷贝到`my_project/cmsis`文件夹下

4.将标准固件库文件目录`.../STM32F4xx_DSP_StdPeriph_Lib_V1.9.0/Libraries/STM32F4xx_StdPeriph_Driver`文件夹下的`inc`和`src`文件夹全部拷贝移植到`lib`文件夹下

5.将标准固件库文件目录`.../STM32F4xx_DSP_StdPeriph_Lib_V1.9.0/Libraries/CMSIS/Device/ST/STM32F4xx/Include`文件夹目录下的`stm32f4xx.h`、`system_stm32f4xx.h`文件拷贝到`include`文件夹下

6.将标准固件文件目录`.../STM32F4xx_DSP_StdPeriph_Lib_V1.9.0/Project/STM32F4xx_StdPeriph_Templates`目录下的`main.c`、`stm32f4xx_it.c`、`system_stm32f4xx.c`拷贝移植到`src`文件夹下，`stm32f4xx_conf.h`、`stm32f4xx_it.h`拷贝到`include`文件夹中，其中main.c文件是STM32工程文件的主函数程序,(main.c 也可以不拷贝，自行创建即可)

7.将标准固件库文件目录`.../STM32F4xx_DSP_StdPeriph_Lib_V1.9.0/Project/STM32F4xx_StdPeriph_Templates/TrueSTUDIO/STM32F40_41xxx`文件夹下的`STM32F417IG_FLASH.ld`拷贝到`my_project`的工程根目录下，并重命名为**stm32_flash.ld**。

## 2.2编写 Makefile

到这里我们需要自行手动编写一个 Makefile...

逗你玩呢，我已经写好了，请慢用：

```Makefile

CROSS_COMPILE = arm-none-eabi-

# 将源文件放在这里 (*.c)
SRCDIR=./src
LIBDIR=./lib/src

SRC = ${wildcard ${LIBDIR}/*.c} \
	  ${wildcard ${SRCDIR}/*.c}

# 将以该名称生成二进制文件 (.elf, .bin, .hex)
PROJECT_NAME=out

# 编译器设置。仅编辑 CFLAGS 以包含其他头文件。
CC = $(CROSS_COMPILE)gcc
AS = $(CROSS_COMPILE)as
LD = $(CROSS_COMPILE)gcc
OBJCOPY = ${CROSS_COMPILE}objcopy

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

# 将 SRC 中每个 .c 文件的扩展名替换为 .o，生成对应的目标文件。例如，如果 SRC 中有一个文件 main.c，它会生成 main.o，以便后续编译和链接时使用这些目标文件。
OBJS = $(SRC:.c=.o)

vpath %.c ./lib/src \
vpath %.c ./src \

.PHONY: proj

all:proj

proj:$(PROJECT_NAME).elf

$(PROJECT_NAME).elf: $(SRC)
	$(CC) $(CFLAGS) $^ -o $@
	$(OBJCOPY) -O ihex $(PROJECT_NAME).elf $(PROJECT_NAME).hex
	$(OBJCOPY) -O binary $(PROJECT_NAME).elf $(PROJECT_NAME).bin

clean:
	rm -f *.o $(PROJECT_NAME).elf $(PROJECT_NAME).hex $(PROJECT_NAME).bin

flash:proj
	st-flash write $(PROJECT_NAME).bin Ox8000000
	STM32_Programmer_CLI -c port=SWD -w $(PROJECT_NAME).hex 

```

将以上内容复制并保存为`Makefile`，将其放到`my_project`工程根目录下

# 3.编译前准备（重要！！！）

完成上面的步骤，恭喜你！已经成功了3/4...

但在正式编译之前，你需要进行以下配置：

1.将`my_project/lib/src`目录下的`stm32f4xx\_fmc.c`文件删除

2.将`my_project/src`目录下的`stm32f4xx\_it.c`文件**第25行**的**main.h头文件引用（#include "main.h"）注释(或删除)**，**137行的延时函数调用（TimingDelay_Decrement();）注释(或删除)**

到这里你就可以通过`make`来成功编译构建项目啦～～～

### 也许有时候遭遇问题

也可能你完成了上面所有的设置，但是在进行`make`时依然遇到了错误，这时候你就可以通过下面的方法解决：

- 将`my_project`目录下的`stm32\_flash.ld`链接文件的75行（也就是`} >FLASH`这一行的上方）添加`_exit = .;`，否则编译会报错（**注意：这是由于交叉编译器版本的问题**）

# 4.去除警告（可选）

直到你兴致勃勃且成功地运行了`make`命令，可惜结果对于稍有洁癖的你来说简直不能容忍，因为出现了一堆警告信息：

```shell
/Applications/ArmGNUToolchain/13.3.rel1/arm-none-eabi/bin/../lib/gcc/arm-none-eabi/13.3.1/../../../../arm-none-eabi/bin/ld: /Applications/ArmGNUToolchain/13.3.rel1/arm-none-eabi/bin/../lib/gcc/arm-none-eabi/13.3.1/../../../../arm-none-eabi/lib/thumb/v7e-m+fp/hard/libg.a(libc_a-closer.o): in function `_close_r':
closer.c:(.text._close_r+0xc): warning: _close is not implemented and will always fail
/Applications/ArmGNUToolchain/13.3.rel1/arm-none-eabi/bin/../lib/gcc/arm-none-eabi/13.3.1/../../../../arm-none-eabi/bin/ld: /Applications/ArmGNUToolchain/13.3.rel1/arm-none-eabi/bin/../lib/gcc/arm-none-eabi/13.3.1/../../../../arm-none-eabi/lib/thumb/v7e-m+fp/hard/libg.a(libc_a-lseekr.o): in function `_lseek_r':
lseekr.c:(.text._lseek_r+0x14): warning: _lseek is not implemented and will always fail
/Applications/ArmGNUToolchain/13.3.rel1/arm-none-eabi/bin/../lib/gcc/arm-none-eabi/13.3.1/../../../../arm-none-eabi/bin/ld: /Applications/ArmGNUToolchain/13.3.rel1/arm-none-eabi/bin/../lib/gcc/arm-none-eabi/13.3.1/../../../../arm-none-eabi/lib/thumb/v7e-m+fp/hard/libg.a(libc_a-readr.o): in function `_read_r':
readr.c:(.text._read_r+0x14): warning: _read is not implemented and will always fail
/Applications/ArmGNUToolchain/13.3.rel1/arm-none-eabi/bin/../lib/gcc/arm-none-eabi/13.3.1/../../../../arm-none-eabi/bin/ld: /Applications/ArmGNUToolchain/13.3.rel1/arm-none-eabi/bin/../lib/gcc/arm-none-eabi/13.3.1/../../../../arm-none-eabi/lib/thumb/v7e-m+fp/hard/libg.a(libc_a-writer.o): in function `_write_r':
writer.c:(.text._write_r+0x14): warning: _write is not implemented and will always fail
/Applications/ArmGNUToolchain/13.3.rel1/arm-none-eabi/bin/../lib/gcc/arm-none-eabi/13.3.1/../../../../arm-none-eabi/bin/ld: warning: out.elf has a LOAD segment with RWX permissions
arm-none-eabi-objcopy -O ihex out.elf out.hex
arm-none-eabi-objcopy -O binary out.elf out.bin
```

所以，有没有解决办法呢？当然有：

在`my_project/src`目录下新建一个`syscalls.c`文件：

```c
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
```

再次执行 `make` 即可。

