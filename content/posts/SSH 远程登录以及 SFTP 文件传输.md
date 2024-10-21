---
title: "SSH 远程登录以及 SFTP 文件传输"
date: 2024-10-21T21:54:13.624552
categories: ['Linux']
author: "Ronan"
---
**SSH 默认端口号是22.**

# SSH 远程登录主机

```shell
ssh username@hostname
```

其中，`username`是用户名，`hostname` 是主机名或IP 地址。

---

# SSH 连接到自定义或映射端口

有时候为了安全以及各种需求，会将ssh 默认端口进行映射或者更改，这时候就可以使用以下命令

```shell
ssh -p <remote_port> username@<hostname>
```

**注意：P 是小写！！！**

其中，`remote_port`是已经映射的远程端口号而不是本地开放默认端口，`username`是用户名，`hostname`是远程主机的主机名活 IP 地址。

---

# SFTP 传输文件到远程主机

**SFTP 默认端口号与 SSH 一致，都是22.**

## 1 连接到主机

```shell
sftp username@hostname
```

sftp连接到远程主机的方式与 ssh 命令相同。

## 2 改变远程目录

```shell
cd 目标路径
```

## 3 改变本地当前工作目录

```shell
lcd 目标路径
```

## 4 上传文件

```shell
put 本地文件路径 [远程目标目录路径]
```

如果未指定远程目标目录路径，此命令会将本地文件上传到当前远程目录。可以使用 `-r`选项递归上传目录。

## 5 下载文件

```shell
get 远程文件路径 [本地目标目录路径]
```

如果未指定本地目标目录路径，此命令将远程文件下载到当前本地工作目录。可以使用`-r` 选项递归上传目录。

---

# SFTP 连接到自定义或映射端口

```shell
sftp -P <remote_port> username@<hostname>
```

**注意：P 是大写！！！**

其中，`remote_port`是已经映射的远程端口号而不是本地开放默认端口，`username`是用户名，`hostname`是远程主机的主机名活 IP 地址。

# 退出SFTP连接

输入`exit`或`quit`并回车
