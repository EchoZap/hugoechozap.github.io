---
title: "ssh 测试"
date: 2024-10-21T21:34:10.267172
categories: ['Docs', 'Tools']
author: "Ronan"
------
title: "ssh 免密登录"
date: 2021-11-01T18:10:14+01:00
categories: ['Travelling', 'Holidays']
tags: ['Support', 'Discussions', 'Q&A', 'Ideas', 'Show and tell']
author: "Ronan"
---

# 1创建密钥

在本地主机终端输入

```shell
ssh-keygen
```

之后一路回车，不出意外的话，你将看到以下内容

![img](https://imgs.ronan.us.kg/ssh-keygen.png)

恭喜，你已经完成第一步！

---

# 2检查密钥是否创建成功

在终端输入

```shell
ls .ssh
```

看到以下内容

![img](https://imgs.ronan.us.kg/ls_.ssh.png)

看到其中有`id_ed25519`(私钥))**其中的ed25519在不同设备可能会有不同，有的可能是id_rsa**、`id_ed25519.pub`(公钥)两个文件，恭喜，你已经完成第二步了，离成功更近了！

---

# 3将公钥复制到远程主机

在终端键入

```shell
ssh-copy-id -i <~/.ssh/id_ed25519.pub> <username>@<remote_ip>
```

其中的`<~/.ssh/id_ed25519.pub>` 是公钥所处的路径，`<username>`是用户名，`<remote_ip>`是主机名或IP 地址。

这条命令会将公钥保存到远程主机的 `~/.ssh/authorized_keys` 文件中。

恭喜你，现在可以不再每次登录都需要输入密码了！！！

---

# 4问题排查

有时候因为之前的服务器重装了系统，但是在本地主机仍然保存着之前的私钥，所以在重新登录远程服务器时会遇到以下问题

```shell
❯ ssh root@<your_remote_ip>

@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ED25519 key sent by the remote host is
SHA256:voGkOAiSibnvAWCsXNasOzsmdMnc2ff7MHE2jsZRfSE.
Please contact your system administrator.
Add correct host key in /Users/iaa/.ssh/known_hosts to get rid of this message.
Offending ED25519 key in /Users/iaa/.ssh/known_hosts:28
Host key for aby.ronan.cloudns.ch has changed and you have requested strict checking.
Host key verification failed.
```

## 4.1解决方法

运行以下命令来删除旧的主机密钥

```shell
ssh-keygen -R <your_remote_ip>
```

之后重复ssh连接即可。