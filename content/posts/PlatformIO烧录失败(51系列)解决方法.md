---
title: "PlatformIO烧录失败(51系列)解决方法"
date: 2024-10-22T13:21:34+08:00
categories: ['Vscode']
author: "Ronan"
---
## 遭遇问题

***platformIO一直卡在烧录程序中，导致开发版一直处于断电状态***
![问题](https://imgs.ronan.us.kg/PIO1.png)

## 解决方法

将main.py里的这一行注释即可
![解决1](https://imgs.ronan.us.kg/PIO2.png)

该文件在以下路径
`~/.platformio/platforms/intel_mcs51/builder/main.py`
![解决2](https://imgs.ronan.us.kg/PIO3.png)
