---
title: "macOS电池管理脚本"
date: 2024-10-22T13:24:23+08:00
categories: ['Macos']
author: "Ronan"
---
将以下内容保存为 `power_manager.sh` 并放入系统环境变量的路径中，接下来可通过 `nohup /path/power_manager.sh &` 来实现后台运行

```bash
#!/bin/zsh

# 关闭休眠
disable_sleep() {
    sudo pmset -b sleep 0
    sudo pmset -b disablesleep 1
}

# 启用休眠
enable_sleep() {
    sudo pmset -b sleep 5
    sudo pmset -b disablesleep 0
}

while true; do

    LOG_FILE="～/power_manager.log"

    # 获取电脑盖子状态
    lid_state=$(ioreg -r -k AppleClamshellState -d 4 | grep AppleClamshellState | head -1 | awk '{print $NF}')

    # 获取电源连接状态
    ac_state=$(pmset -g batt | head -1)

    if [[ $lid_state == "Yes" ]]; then
    # 合盖状态
        if [[ $ac_state =~ "AC" ]]; then
            # 连接电源，关闭休眠并立即保持系统唤醒
            disable_sleep
        else
            # 未连接电源，开启休眠并立即进入休眠
            enable_sleep
            pmset sleepnow
        fi
    else
    # 开盖状态
        if [[ $ac_state =~ "AC" ]]; then
            # 连接电源，关闭休眠并保持系统唤醒
            disable_sleep
        else
            # 未连接电源，开启休眠但不立即进入休眠
            enable_sleep
        fi
    fi
    sleep 5
done
```
