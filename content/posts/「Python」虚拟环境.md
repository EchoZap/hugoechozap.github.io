---
title: "「Python」虚拟环境"
date: 2024-10-21T21:55:49.267776
categories: ['Docs']
author: "Ronan"
---
*在虚拟环境中，pip 和 pip3 通常会指向同一个 Python 版本的包管理器。虚拟环境会自动配置 pip 指向该环境中的 Python 解释器。*  

# 创建虚拟环境：
```
python3 -m venv myenv
```  
> [!NOTE]
> *此方法创建的虚拟环境基于系统自带的python版本，不能创建系统未安装的python版本的虚拟环境。假设系统的python3版本是3.12，那么通过 `-m venv` 创建的虚拟环境中的python也会是3.12版本的*   

**如果要创建系统本身未安装的python版本虚拟环境，可以通过 [conda](https://blog.ronan.us.kg/post/Conda-chang-yong-yong-fa.html) 实现**

# 激活虚拟环境：

1.对于 Bash 或 Zsh：
```
source myenv/bin/activate
```

2.对于 Fish shell：
```
source myenv/bin/activate.fish
```

3.对于 Windows 命令提示符：
```
myenv\Scripts\activate.bat
```

4.对于 Windows PowerShell：
```
myenv\Scripts\Activate.ps1
```

# 检查pip和pip3指向的路径：
激活虚拟环境后，运行以下命令来检查 pip 和 pip3 的路径：
```
which pip
which pip3
```
在虚拟环境中，这两个命令通常会输出相同的路径，例如：
```
/path/to/your/venv/bin/pip
/path/to/your/venv/bin/pip3
```

# 检查pip和pip3版本：
你也可以检查pip和pip3的版本号，它们应该指向相同的Python版本：
```
pip -V
pip3 -V
```
输出应该类似于：
```
pip 20.2.3 from /path/to/your/venv/lib/python3.8/site-packages/pip (python 3.8)
pip 20.2.3 from /path/to/your/venv/lib/python3.8/site-packages/pip (python 3.8)
```

<!-- ##{"timestamp":1723496120}## -->
