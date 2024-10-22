---
title: "Linux查看或修改主机名"
date: 2024-10-22T13:26:21+08:00
categories: ['Linux']
author: "Ronan"
---
# 1查看当前主机名

```plain
hostname
```

这将会显示当前的主机名，或者

```zsh
cat /etc/hostname
```
  

# 2修改主机名

使用以下命令设置新主机名

```zsh
sudo hostnamectl set-hostname <new_hostname>
```

之后在文件中找到包含旧主机名的行，并将其替换为新主机名。确保将新主机名映射到正确的 IP 地址上。

```shell
sudo vim /etc/hosts
```

将旧主机名替换为新主机名 *(可选)*

```shell
127.0.0.1   localhost
127.0.1.1   <your-new-hostname>
```

最后，重启一下网络服务

```zsh
sudo systemctl restart networking
```

**注意：如果是通过ssh登录的，在完成以上步骤之后可能需要退出后重新登录才可看到主机名刷新！！！**