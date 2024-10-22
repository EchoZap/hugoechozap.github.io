---
title: "「python」查看或切换当前解释器"
date: 2024-10-22T13:26:26+08:00
categories: ['Linux']
author: "Ronan"
---
# 查看系统中所有安装的 Python 解释器
```
which -a python python3
```
这会列出系统路径中找到的 python 和 python3 解释器的位置:
```
❯ which -a python python3

python not found
/usr/bin/python3
/opt/homebrew/bin/python3
```
`-a` 选项确保显示所有匹配的路径，而不仅仅是第一个。



# 确定系统上当前正在使用的 Python 解释器
```
which python
which python3
```
这将显示出系统当前 python 和 python3 命令指向的实际路径。



# 切换系统上当前正在使用的 Python 解释器
有以下几种方式，根据需要选择
### 1.更新 PATH 环境变量
根据你使用的 Shell 类型，打开相应的配置文件。例如，对于 zsh 用户，编辑 ~/.zshrc 文件；对于 bash 用户，编辑 ~/.bash_profile 或 ~/.bashrc 文件。  
确保 /usr/bin 在 PATH 变量中位于其他 Python 解释器路径之前。例如，将 /usr/bin 放在最前面：
```
export PATH="/usr/bin:/opt/homebrew/bin:$PATH"
```
**注意：在这行之后不能有任何关于`/opt/homebrew/bin:$PATH`的环境变量设置，先后顺序不能乱，因为shell配置文件从上往下执行。如果不，则切换设置不生效**


### 2.使用 alias 命令
在~/.zshrc 或 ~/.bashrc 中添加以下设置
```
alias python3='/usr/bin/python3'
```
根据需要可以将`/usr/bin/python3`更换为所需。


# 切换vscode正在使用的python解释器
1、Ctrl + Shift + p 打开命令行面板
2、输入 `Python:Select Interpreter` 命令，配置默认的解释器 
3、默认解释器生效


# 确保当前pip或pip3命令使用的是正确的Python解释器
1.确认当前pip3命令对应的Python解释器：
```
which pip
which pip3
```

2.查看当前pip3的Python路径：
```
pip3 -V
```

3.创建一个新的别名
如果pip3的路径不正确，可以手动创建一个新的别名，使其与正确的python3解释器匹配。假设你要使用系统自带的Python解释器，可以这样操作：
```
alias pip3='/usr/bin/python3 -m pip'
```



# 通过pip安装的程序`command not found`
已经明确通过pip安装，使用`pip list`列出已经安装的库或程序
```
❯ pip list
Package    Version
---------- -------
altgraph   0.17.2
stcgal     1.10
```

但是在运行时候出现错误：
```
❯ stcgal
zsh: command not found: stcgal
```
### 解决方法
1.找到stcgal的安装路径：
```
pip show stcgal
```

2.将该目录添加到你的PATH环境变量中：
如果stcgal安装在一个Python环境的bin目录中，需要将该目录添加到你的PATH环境变量中。例如，如果安装路径是/Users/iaa/Library/Python/3.9/bin，你可以这样做：
```
export PATH=$PATH:/Users/iaa/Library/Python/3.9/bin
```

