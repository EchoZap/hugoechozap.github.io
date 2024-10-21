---
title: "Linux用户与用户组的创建、切换及删除"
date: 2024-10-21T21:54:15.512076
categories: ['Linux']
author: "Ronan"
---
# 1创建新用户

1. 以root用户身份登录到Linux系统。
2. 打开终端窗口。
3. 运行以下命令来创建新用户（假设要创建的用户名为newuser）：

```plain
sudo adduser <newuser>
```

## 1.1为新用户设置密码

```shell
sudo passwd <newuser>
```

## 1.2切换到新用户

```shell
su - <newuser>
```

---

# 2将用户添加到sudo组

## 2.1ubuntu

接下来，将新用户添加到sudo组，以便其拥有sudo权限。运行以下命令：（假设要添加的用户名为newuser）

```shell
sudo usermod -aG sudo <newuser>
```

## 2.2CentOS

在CentOS系统中，默认情况下，`sudo` 组并不存在。相反，CentOS使用的是 `wheel` 组来管理具有 `sudo` 权限的用户。你可以将用户添加到 `wheel` 组来解决这个问题。以下是解决步骤：

1.**将用户添加到** **​`wheel`​** **组**：

```shell
sudo usermod -aG wheel <newuser>
```

2.**验证用户是否已被添加到** **​`wheel`​** **组**：

```shell
groups iaa
```

3.**配置** **​`sudoers`​** **文件**（可选，通常默认配置已经包含了 `wheel` 组的 sudo 权限）：

```shell
sudo visudo
```

确保以下行(`%wheel`这一行之前没有`#`符号)未被注释掉：

```shell
## Allows people in group wheel to run all commands
%wheel  ALL=(ALL)       ALL
```

**如果是远程登录，完成以上步骤后，请断开ssh连接后重新登录。**

---

# 3将已存在用户移出sudo组

要将一个已存在的用户移出sudo组，你可以使用`deluser`命令（如果你使用的是Debian或Ubuntu等基于Debian的系统），或者`usermod`命令。以下是使用这两种方法的示例：

### 方法一：使用`deluser`命令（Debian/Ubuntu）

```bash
sudo deluser username sudo
```

这会将用户从sudo组中移出。

### 方法二：使用`usermod`命令

```bash
sudo usermod -G "" username
```

这会将用户的附加组（-G参数后的空字符串）设置为空，从而将用户从sudo组中移出。

无论你使用哪种方法，确保替换`username`为你要移出sudo组的实际用户名。移出sudo组后，该用户将失去以sudo权限执行命令的能力。

---

# 4删除已存在用户

如果用户 "user" 已经存在并且你想删除它，你可以按照以下步骤进行：

1. 以root用户身份登录到Linux系统。
2. 打开终端窗口。
3. 运行以下命令来删除用户 "user"：

```plain
sudo deluser <user>
```

1. 如果你想同时删除用户的家目录和邮件箱，你可以使用以下命令：

```plain
sudo deluser --remove-all-files <user>
```

这将删除用户 "user" 并清理掉相关的家目录和邮件箱。

---

# 5查看用户所属组

1. **groups命令**：这个命令会列出用户所属的用户组。

```bash
groups username
```

将 `username` 替换为您要查看权限的用户的用户名。

1. **id命令**：这个命令会显示用户和组的数字标识。

```bash
id username
```

这将显示有关用户的更多详细信息，包括所属的组。

1. **getent命令**：这个命令可以从系统数据库中检索用户和组的信息。

```bash
getent group | grep username 
```

1.  **/etc/passwd和/etc/group文件**：您可以手动查看这些文件来了解用户和组的信息。

```bash
grep username /etc/passwd
grep username /etc/group
```

这将在相应的文件中查找特定用户的条目。