---
title: "mac以指纹授权sudo指令"
date: 2024-10-22T13:24:16+08:00
categories: ['Macos']
author: "Ronan"
---
使用以下命令打开；

```zsh
sudo vim /etc/pam.d/sudo
```

出现以下内容：

```zsh
  1 # sudo: auth account password session
  2 auth       include        sudo_local
  3 auth       sufficient     pam_smartcard.so
  4 auth       required       pam_opendirectory.so
  5 account    required       pam_permit.so
  6 password   required       pam_deny.so
  7 session    required       pam_permit.so
```


将下面这一行

```plain
auth sufficient pam_smartcard.so
```

修改为

```plain
auth sufficient pam_tid.so
```

保存并退出即可
