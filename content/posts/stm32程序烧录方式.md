---
title: "stm32程序烧录方式"
date: 2024-10-21T21:56:37.441724
categories: ['Docs']
author: "Ronan"
---
> 以下以stm32f103c8t6芯片为例

# 使用stlink

```bash
st-flash write /path/project/build.bin 0x08000000
```

注意：

- 使用 `st-flash` 命令需要提前安装 stlink ，macOS 通过`brew install stlink`即可安装。
- 烧写 `.bin` 文件需要指定 flash 起始地址，也就是 `0x08000000`(具体芯片具体地址根据手册修改)

# 使用openocd

```bash
openocd -f interface/stlink-v2.cfg -f target/stm32f1x.cfg -c "program /path/project/build.bin verify reset exit 0x08000000"
```

注意：

- 根据自己的stlink 实际版本更改`interface/stlink-v2.cfg`为适配的 stlink 配置文件
- 根据自己的stm32单片机实际型号更改`target/stm32f1x.cfg`为适配的配置文件

# 使用STM32_Programmer_CLI（串口烧录）

使用该方法之前需要安装`STM32_Programmer_CLI`，该程序可以到 ST（意法半导体）官网自行下载，需要将其添加到环境变量。之后将串口线正确接到板子上的引脚，通过以下命令将程序文件烧录到单片机中。

```bash
STM32_Programmer_CLI -c port=/dev/tty.usbmodem11403 -w /path/project/build.bin 0x08000000
```

同样的，烧录.bin 文件需要指定 flash 起始地址。


注意：

- `port=`后面是串口号，mac 以及 linux 可以通过`ls /dev/tty.*`命令来查看串口号，Windows 一般是`COMx`，可以通过`设备管理器`查看。
