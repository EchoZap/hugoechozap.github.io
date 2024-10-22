---
title: "「platformIO」基于 stm32 HAL库 的工程模板"
date: 2024-10-22T13:28:26+08:00
categories: ['Docs']
author: "Ronan"
---
# 1.建立 Makefile 工程

通过 STM32CubeMX 建立适于自己开发版的 Makefile 工程（步骤不赘述，百度一下，你就知道），并且记住「**工程名**」和「**工程存放路径**」。

# 2.新建 platformIO 工程

新建 platformIO 工程时的「Framework」**一定要选择「STM32Cube」**，新建工程的「**工程名**」和「**工程存放路径**」要与之前建立的 Makefile 工程**一致**。

工程建立完成后，你应该会得到如下项目结构：

```shell
├── Core
│   ├── Inc
│   │   ├── gpio.h
│   │   ├── main.h
│   │   ├── ...
│   └── Src
│       ├── gpio.c
│       ├── main.c
│       ├── ...
├── Drivers
│   ├── CMSIS
│   │   ├── Device
│   │   │   └── ST
│   │   │       └── STM32F1xx
│   │   │           ├── Include
│   │   │           │   ├── stm32f103xb.h
│   │   │           │   ├── stm32f1xx.h
│   │   │           │   └── system_stm32f1xx.h
│   │   │           ├── LICENSE.txt
│   │   │           └── Source
│   │   │               └── Templates
│   │   ├── Include
│   │   │   ├── cmsis_armcc.h
│   │   │   ├── cmsis_armclang.h
│   │   │   ├── cmsis_compiler.h
│   │   │   ├── cmsis_gcc.h
│   │   │   ├── ...
│   │   └── LICENSE.txt
│   └── STM32F1xx_HAL_Driver
│       ├── Inc
│       │   ├── Legacy
│       │   │   └── stm32_hal_legacy.h
│       │   ├── stm32f1xx_hal.h
│       │   ├── stm32f1xx_hal_cortex.h
│       │   ├── stm32f1xx_hal_def.h
│       │   ├── ...
│       ├── LICENSE.txt
│       └── Src
│           ├── stm32f1xx_hal.c
│           ├── stm32f1xx_hal_cortex.c
│           ├── stm32f1xx_hal_dma.c
│           ├── ...
├── Makefile
├── STM32F103C8Tx_FLASH.ld
├── include
│   └── README
├── lib
│   └── README
├── one.ioc
├── platformio.ini
├── src
├── startup_stm32f103xb.s
└── test
    └── README
```

# 3.配置工程

- 修改 platformio.ini

在 **platformIo.ini**中添加：

```shell
;烧录方式，有 jlink、stlink 以及 serial 等方式，根据自身情况选择
upload_protocol = stlink
;调试工具选择
debug_tool = stlink

[platformio]
src_dir = Core/Src
include_dir = Core/Inc
```

- 精简结构

删除工程根目录下的「src」和「include」文件夹

好了！现在点击 **build** ，没有报错，可以完美运行了！！！

# 4.添加自己的库

在 platformIO 建立的工程中，根目录下有一个 lib 文件夹，这里就可以存放自己的库。

示例：假设现在有一个 led.c 和 led.h，那么你就可以**在 lib 目录中新建一个 led 目录**，然后将 led.c 和 led.h 放到 ./lib/led 中，之后，在 ./Core/Src/main.c 中直接导入 led.h

```c
#include <led.h>

int main (void)
{
  ...
}

```

# 5.遭遇问题

> 以 stmf103c8t6为例

- 在第一次使用 stlink-v2 烧录时遇到以下问题：

```
stlink-xPack Open On-Chip Debugger 0.12.0-01004-g9ea7f3d64-dirty (2023-01-30-17:03)
Licensed under GNU GPL v2
For bug reports, read
        http://openocd.org/doc/doxygen/bugs.html
debug_level: 1

hla_swd
Warn : UNEXPECTED idcode: 0x2ba01477
Error: expected 1 of 1: 0x1ba01477
in procedure 'program'
** OpenOCD init failed **
shutdown command invoked
```

- 分析原因  
  这是因为 stm32f1x.cfg 文件期望的 stm32f1x 芯片 ID 应该是 0x1ba01477 ，但是实际扫描到的设备 id 是 0x2ba01477。

- 问题解决  
  platformIO 使用的 openocd 是其自行打包下载而不是用户自行下载的第三方包，所以，首先要找到 `/Users/<your_username>/.platformio/packages/tool-openocd/openocd/scripts/target/stm32f1x.cfg` 并打开，在其中找到以下

```
#jtag scan chain
if { [info exists CPUTAPID] } {
   set _CPUTAPID $CPUTAPID
} else {
   if { [using_jtag] } {
      # See STM Document RM0008 Section 26.6.3
      set _CPUTAPID 0x3ba00477
   } {
      # this is the SW-DP tap id not the jtag tap id
      set _CPUTAPID 0x1ba01477
   }
}
```

我们可以看到罪魁祸首就在这里： 

```
# this is the SW-DP tap id not the jtag tap id
set _CPUTAPID 0x1ba01477
```

最后，我们只需要把 **set _CPUTAPID** 后面的 **0x1ba01477** 修改为报错信息里的 **0x2ba01477** ， clean -> build -> upload 一气呵成，再也没有标红的让人狂飙血压的玩意了！
 
