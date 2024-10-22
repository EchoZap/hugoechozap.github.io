---
title: "vscode + stm32CubeMX + Makefile工程以及问题解决"
date: 2024-10-22T13:28:37+08:00
categories: ['Docs']
author: "Ronan"
---
通过 STM32CubeMX 建立的 Makefile 工程，应该会有以下结构：

```shell
❯ tree test
test
├── Core
│   ├── Inc
│   └── Src
├── Drivers
│   ├── CMSIS
│   │   ├── Device
│   │   │   └── ST
│   │   │       └── STM32F1xx
│   │   │           ├── Include
│   │   │           ├── LICENSE.txt
│   │   │           └── Source
│   │   │               └── Templates
│   │   ├── Include
│   │   └── LICENSE.txt
│   └── STM32F1xx_HAL_Driver
│       ├── Inc
│       │   ├── Legacy
│       ├── LICENSE.txt
│       └── Src
├── Makefile
├── STM32F103C8Tx_FLASH.ld
├── build
├── startup_stm32f103xb.s
└── test.ioc
```

# 1.配置本机环境

为了能顺利使用通过 STM32CubeMX 建立的 Makefile 工程，你需要先安装完成以下准备：

- 安装 `arm-none-eabi-gcc` 交叉编译链
- 安装 `openOCD`
- 安装 `Make`

安装 `arm-none-eabi-gcc` 交叉编译链：

- windows：
  - 链接: [https://caiyun.139.com/m/i?145CFu8T4pzZ9](https://caiyun.139.com/m/i?145CFu8T4pzZ9)
  - 提取码:ifpa
    
- linux：
  - 链接: [https://caiyun.139.com/m/i?145CFu8QjVMxO](https://caiyun.139.com/m/i?145CFu8QjVMxO)
  - 提取码:sXCp
  
- macOS：
  - 链接: [https://caiyun.139.com/m/i?145CFaDB85q9C](https://caiyun.139.com/m/i?145CFaDB85q9C)
  - 提取码:OoEr
  
如果你是个 ~~大佬~~ ,当然大佬应该是不会看这种教程的，你喜欢自己构建的话，可以用下面的源码：

- 链接: [https://caiyun.139.com/m/i?145CGY0p8xYON](https://caiyun.139.com/m/i?145CGY0p8xYON)
- 提取码:t3bC

下载安装好编译链之后，将其放入到环境变量 `PATH` 中(至于具体步骤，百度一下，你就知道)


# 2.创建工程并去掉警告(红色波浪线)

> vscode 需要提前安装 C/C++ 插件

1.新建一个 STM32CubeMX_projects 目录(以后的工程就统一放置在该目录下，建议不要放置在桌面，放在自己容易记忆的位置)，然后使用 vscode 打开该目录，并且将其保存为工作区，将工作区文件保存在希望的位置，以后可以通过双击该文件直接打开 STM32CubeMX_projects 工作区：

![设立工作区](https://imgs.ronan.us.kg/vscode_stm32_makefile_config1.png)


2.在 STM32CubeMX 配置好工程，点击左侧选项卡，然后勾选绿色框里的选项选择 Makefile 导出：

![建立并导出工程](https://imgs.ronan.us.kg/cubemx_makefile_project1.png)
![建立并导出工程](https://imgs.ronan.us.kg/cubemx_makefile_project2.png)

3.使用 vscode 打开该工程目录，点开`./Core/Src/main.c`，你会发现：满是令人高血压的红色波浪线，难以忍受。

![红色波浪线](https://imgs.ronan.us.kg/vscode_stm32_makefile_config2.png)

---

解决方法：

> 动手能力比较强的可以通过 python 安装一个 compiledb ，之后在工程根目录中运行 `compiledb -n make` 命令，这将在工程根目录生成一个 `compile_commands.json` ，vscode 会根据该文件自动解析文件关系，小白可以忽略...请看下面：

1.在工作区根目录中按下(macos)`cmd+shift+p`，(windows)`ctrl+shift+p`，输入 `C/C++` 然后点击`Edit Configurrations(JSON)`选择

![创建 cpp_json](https://imgs.ronan.us.kg/vscode_stm32_makefile_config3.png)

2.接下来 vscode 会在你的工作区根目录下创建一个 .vscode 目录和一个 c_cpp_properties.json ，默认情况下你在 c_cpp_properties.json 会看到以下内容

![cpp_json](https://imgs.ronan.us.kg/vscode_stm32_makefile_config4.png)

3.打开工程根目录的`Makefile`，找到`#C defines`（也就是宏定义这部分），然后`复制绿色框里的内容`

![Makefile](https://imgs.ronan.us.kg/vscode_stm32_makefile_config5.png)

4.回到`c_cpp_properties.json`，将上面复制的内容按 `以下格式(一定要注意格式)` 粘贴到`"defines"`的中括号里，就像下面这样：

![c_cpp_properties.json](https://imgs.ronan.us.kg/vscode_stm32_makefile_config6.png)

5.打开工程根目录的`Makefile`，找到`#C includes`（也就是头文件搜索这部分），然后`复制绿色框里的内容`

![Makefile](https://imgs.ronan.us.kg/vscode_stm32_makefile_config7.png)

6.回到`c_cpp_properties.json`，将上面复制的内容按 `以下格式(一定要注意格式)` 粘贴到`"includePath"`的中括号里。自带的第一行不要删除，在后面加上英文的逗号即可。将复制的内容前面的 -D 替换为工程名，这里的工程名是test，将所有内容使用英文引号包裹，每一行后面使用英文逗号结尾，最后一行不使用逗号，就像下面这样：

![c_cpp_properties.json](https://imgs.ronan.us.kg/vscode_stm32_makefile_config8.png)

> 以后每新增加一个工程都可用一样的方法，区别是替换的 `-D` 的工程名要修改为你希望添加的工程名，如果红色警告仍然存在，可能是重复包含，所以你可以在`c_cpp_properties.json` 中的 `"includePath"` 将之前添加的所有 include 路径注释掉，然后只保留你当前聚焦的工程 include 路径即可

![c_cpp_properties.json](https://imgs.ronan.us.kg/vscode_stm32_makefile_config8.1.png)

# 3.工程调试
### 3.1使用 openocd + stlink 调试

> 以stm32f103举例，**注意：使用该方法调试需要安装 opencod 以及配置者具备较深的专业知识**

1.在终端输入

```shell
openocd -f interface/stlink-v2.cfg -f target/stm32f1x.cfg
```

2.**保持上面的终端不要退出，然后开启一个新的终端窗口**，输入以下命令 `arm-none-eabi-gdb -q path/build/<your_project>.elf` 就可以进入 gdb 调试：

```shell
❯ arm-none-eabi-gdb -q path/build/<your_project>.elf
Reading symbols from .../build/<your_project>.elf...
(gdb)
```

接着输入`target remote: 3333`，看见以下内容就可以开始调试了:

```zsh
❯ arm-none-eabi-gdb -q path/build/<your_project>.elf
Reading symbols from ./build/test.elf...
(gdb) target remote: 3333
Remote debugging using : 3333
```

### 3.2使用 vscode 的 cortex-debug 插件调试

> 使用此方法，vscode 需要安装 cortex-debug 插件

先看效果;

![debug](https://imgs.ronan.us.kg/vscode_stm32_debug.png)

实现方法：

在工作区根目录的`.vscode`目录（如果没有该目录可自行创建）中新建 `launch.json` 和 `tasks.json`。

launch.json:

```json
{
    "version": "0.2.0",

    "configurations": [
        {
            "name": "Cortex Debug",
            "cwd": "${workspaceRoot}",
            "executable": "${workspaceFolder}/${input:projectName}/build/${input:projectName}.elf",//根据自己工程实际生成最终.elf 路径修改
            "request": "launch",
            "type": "cortex-debug",
            "servertype": "openocd",
            "configFiles": [  //根据自己开发版以及调试器修改
                "interface/stlink-v2.cfg",
                "target/stm32f1x.cfg"
            ],
            "armToolchainPath": "/opt/arm-none-eabi/bin",
            "preLaunchTask": "stm32 build",
        }
    ],

    "inputs": [
        {
            "id": "projectName",
            "type": "promptString",
            "description": "请输入你要调试的工程名",
        }
    ]
}
```

tasks.json:

```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "stm32 build",
            "type": "shell",
            "options": {
                "cwd": "${workspaceFolder}/${input:projectName}"
            },
            "command": "make",
            "detail": "任务用于构建 STM32 项目"
        }
    ],
    "inputs": [
        {
            "id": "projectName",
            "type": "promptString",
            "description": "请输入构建的工程名(应与调试的工程名一致)",
        }
    ]
}
```

然后运行一次 `make clean` 再重新 `make` 或者 `make DEBUG=1` ，再按下`f5`或者在 vscode 顶栏依次点击`运行`->`启用调试` 即可。

### 3.3调试遭遇问题

调试一直卡在 `HAL_Init` 函数里...

![issue](https://imgs.ronan.us.kg/vscode_stm32_debug_issue1.png)

![issue](https://imgs.ronan.us.kg/vscode_stm32_debug_issue2.png)

---

解决方法

1.打开`.../<Your_Project>/Core/Src/stm32f1xx_hal_msp.c`，在其中找到`HAL_MspInit`函数，默认情况下，函数内部是这样的：

```c
void HAL_MspInit(void)
{
  __HAL_RCC_AFIO_CLK_ENABLE();
  __HAL_RCC_PWR_CLK_ENABLE();
  __HAL_AFIO_REMAP_SWJ_NOJTAG();
}
```

2.将`HAL_MspInit`函数内部的`__HAL_RCC_PWR_CLK_ENABLE()`和`__HAL_AFIO_REMAP_SWJ_NOJTAG()`这两个函数注释，就像这样：

```c
void HAL_MspInit(void)
{
  __HAL_RCC_AFIO_CLK_ENABLE();
  // __HAL_RCC_PWR_CLK_ENABLE();
  // __HAL_AFIO_REMAP_SWJ_NOJTAG();
}
```

然后 `打开终端把之前的调试任务全部停止` -> `再次 make ` -> `重新启动调试` 。

> 如需要减少编译优化等级，可以进入makefile，在 `if DEBUG` 判断的编译流程添加 `-O0` ,之后重新 `make DEBUG=1` 


