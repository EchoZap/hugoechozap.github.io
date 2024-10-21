---
title: "mac合盖不休眠、安装包损坏解决办法"
date: 2024-10-21T21:53:55.034343
categories: ['Macos']
author: "Ronan"
---
# "app已损坏，无法打开"问题解决方法

```shell
sudo xattr -rd com.apple.quarantine <your_app_path>
```

将`your_app_path`替换成app路径，可以直接将应用程序里的app图标拖到终端窗口的这条命令里，按回车键执行。  

# 合盖不休眠
我们可以借助终端命令来实现这一点，打开终端，然后根据需要输入以下命令  
禁用Lid-Sleep的命令（保持系统唤醒）：

```bash
sudo pmset -b sleep 0; sudo pmset -b disablesleep 1
```

激活LId-Sleep的命令（让系统恢复正常休眠）：

```bash
sudo pmset -b sleep 5; sudo pmset -b disablesleep 0
```

由于这些命令是sudo 命令，因此需要您的密码才能运行