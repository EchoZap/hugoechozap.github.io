---
title: "vscode搭建用于8051单片机的sdcc环境"
date: 2024-10-22T13:21:32+08:00
categories: ['Vscode']
author: "Ronan"
---
# 查找sdcc以及8051头文件位置

```shell
❯ which sdcc

/opt/homebrew/bin//sdcc
```

这将列出sdcc的安装位置。接下来找到关于MCS51的头文件

```shell
❯ find /opt/homebrew -name "8051.h"

/opt/homebrew/Cellar/sdcc/4.4.0/share/sdcc/include/mcs51/8051.h
```

# #include <8051.h> 报错解决方案

接下来，我们需要配置 VSCode 的 IntelliSense 以包含头文件目录。

#### 1 创建或编辑 `c_cpp_properties.json`

打开你的项目，然后创建或编辑 `.vscode/c_cpp_properties.json` 文件，并添加头文件目录。

例如，假设头文件在 `/opt/homebrew/Cellar/sdcc/<版本号>/share/sdcc/include/mcs51/`，你可以这样配置：

```json
{
    "configurations": [
        {
            "name": "Mac",
            "includePath": [
                "${workspaceFolder}/**",
                "/opt/homebrew/Cellar/sdcc/<版本号>/share/sdcc/include/mcs51/"
            ],
            "defines": [],
            "macFrameworkPath": [],
            "compilerPath": "/opt/homebrew/bin/sdcc",
            "cStandard": "c11",
            "cppStandard": "c++17",
            "intelliSenseMode": "macos-clang-x64"
        }
    ],
    "version": 4
}
```

请将 `<版本号>` 替换为实际的 SDCC 版本号。

#### 2.重启 VSCode

完成配置后，重启 VSCode 以应用更改。

# 编译代码

```shell
sdcc main.c
```

之后会获得一个`.ihx`文件，通过以下命令将其链接为`hex`文件

```shell
packihx (your_main.ihx) > (your_main.hex)
```

# 烧录程序

> [!note]
> 烧录程序使用的是stcgal  
可以通过 `pip install stcgal` 来安装

首先将mac连接到单片机，之后在终端键入，会得到烧录串口号`/dev/tty.wchusbserial1140`或者`/dev/tty.usbserial-1140`(该串口号每一次连接可能会更改,所以在烧录前最好通过该命令检查)

```shell
❯ ls /dev/tty.*

/dev/tty.Bluetooth-Incoming-Port        /dev/tty.usbserial-1140                 /dev/tty.wlan-debug
/dev/tty.Ronan                          /dev/tty.wchusbserial1140
```

然后通过以下命令烧录

```shell
stcgal -p <port> -b <baud_rate> <your_program.hex>
```

`<port>`:就是上面得到的串口号`/dev/tty.wchusbserial1140`

`<baud_rate>`:波特率，如9600、19200、38400、57600、115200等。

```shell
stcgal -p /dev/tty.wchusbserial1130 -P stc89 -b 115200 main.hex 
 
usage: stcgal [-h] [-e] [-a] [-A {dtr,rts}] [-r RESETCMD]
              [-P {stc89,stc89a,stc12a,stc12b,stc12,stc15a,stc15,stc8,stc8d,stc8g,usb15,auto}] [-p PORT] [-b BAUD] [-l HANDSHAKE]
              [-o OPTION] [-t TRIM] [-D] [-V]
              [code_image] [eeprom_image]
stcgal: error: argument code_image: can't open 'stc89': [Errno 2] No such file or directory: 'stc89'
```