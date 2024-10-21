---
title: "「国内解决方法」linux安装docker"
date: 2024-10-21T21:54:12.752117
categories: ['Linux']
author: "Ronan"
---
# 1.安装Docker

一键安装命令

```
sudo curl -fsSL https://github.com/tech-shrimp/docker_installer/releases/download/latest/linux.sh| bash -s docker --mirror Aliyun
```

> 备用（如果Github访问不了，可以使用Gitee的链接）<br>

```
sudo curl -fsSL https://gitee.com/tech-shrimp/docker_installer/releases/download/latest/linux.sh| bash -s docker --mirror Aliyun
```

# 2.镜像拉取

```zsh
bash -c "$(curl -sSLf https://xy.ggbond.org/xy/docker_pull.sh)" -s 完整镜像名
```
