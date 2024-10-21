---
title: "「python」Conda虚拟环境"
date: 2024-10-21T21:56:52.698084
categories: ['Docs']
author: "Ronan"
---
# 创建一个新的虚拟环境

可以使用以下命令创建一个名为 `jupyter_env` 的新环境，并指定 Python 版本（例如 Python 3.11）

```shell
conda create -n jupyter_env python=3.11
```

> [!tip]
>
> 通过上面命令创建的虚拟环境通常会保存在主环境目录，如果想指定虚拟环境的位置，可以使用下面的命令

**指定路径**：如果你在创建环境时使用了 `-p` 或 `--prefix` 选项指定路径，环境会存储在你指定的位置。

```shell
conda create -p /my/custom/path/env_name python=3.11
```



# 激活环境与退出环境

激活新创建的名为 `jupyter_env` 的环境：

```shell
conda activate jupyter_env
```

退出已激活环境：

```shell
conda deactivate
```

# 列出所有虚拟环境

首先，你可以列出所有已创建的虚拟环境(以下命令二选一)：

```bash
conda env list
```

```bash
conda info --envs
```

这会显示所有环境的名称和路径。


# 删除虚拟环境

- 删除指定名称的虚拟环境：

```bash
conda remove --name ENV_NAME --all
```

其中 `ENV_NAME` 是你要删除的环境的名称，`--all` 选项表示删除整个环境及其所有包和依赖项。

- 删除指定路径的虚拟环境

如果你是通过指定路径创建的环境，可以使用路径来删除：

```bash
conda remove -p /path/to/your/env --all
```

在执行删除命令后，Conda 会提示你确认是否要删除该环境。输入 `y` 并回车以确认删除操作。


# 查看当前处于哪一个虚拟环境

你可以使用以下命令来查看当前的 Conda 环境信息：

```bash
conda info --envs
```

这个命令会列出所有环境及其路径，并且在当前激活的环境前面会有一个星号 `*` 标记。例如：

```bash
# conda environments:
#
base                  *  /opt/homebrew/Caskroom/miniconda/base
jupyter_env               /opt/homebrew/Caskroom/miniconda/base/envs/jupyter_env
```

在上面的示例中，`base` 环境被标记为当前激活的环境。

你还可以检查当前使用的 Python 解释器的路径，确保它位于 Conda 环境的路径下。


# 虚拟环境默认安装位置

### 查看 Conda 所有的虚拟环境路径

运行以下命令来检查 Conda 的环境路径配置：

```shell
conda config --show envs_dirs
```

这个命令会列出 Conda 查找虚拟环境的所有路径。可能会出现多个路径：

主环境目录：

- 如果没有指定路径，环境会默认存储在 Conda 安装目录下的 `envs` 文件夹（例如 `/opt/homebrew/Caskroom/miniconda/base/envs`）。

用户环境目录：

- 在某些情况下，例如用户没有写入权限或主环境目录已满，Conda 可能会选择将环境存储在用户主目录下的 `.conda/envs` 目录中。

### 设置默认环境存储路径

你可以通过 `conda config` 命令来设置默认的环境存储路径，这样以后所有不通过 -p 指定路径的虚拟环境都会安装到以下目录。例如，设置所有新环境安装在某个指定目录下：

```bash
conda config --add envs_dirs /my/custom/path
```

这将把 `/my/custom/path` 添加到环境路径的优先级列表中。

### 删除环境存储路径

要删除 Conda 中的某个环境路径（envs_dirs），找到并打开 Conda 配置文件， Conda 的配置文件通常位于 ~/.condarc 路径。你可以使用任意文本编辑器来编辑它，例如：

编辑 envs_dirs 配置： 在 .condarc 文件中找到类似以下的部分：

```
envs_dirs:
  - /opt/homebrew/Caskroom/miniconda/base/envs
  - /Users/iaa/bin/myenv
  - /Users/iaa/.conda/envs
```

删除你不需要的路径，例如，如果你想删除 /Users/iaa/bin/myenv，就把这一行删掉，然后保存退出即可。


# 鬼东西总会自动激活base环境怎么办
只需要在 `.zshrc的最后一行或者 conda 初始化的后面` 添加：

```shell
# 阻止conda base 环境的自动激活
conda config --set auto_activate_base false
```

然后保存 `source ~/.zshrc` 即可

# 自动激活特定虚拟环境
假设我现在有一个有一个 myenv 的虚拟环境，要让 Conda 自动激活 myenv 环境（就像自动激活 base 环境一样）：

1.禁用 base 环境的自动激活（如果尚未禁用）：

```
conda config --set auto_activate_base false
```

2.修改 ~/.zshrc 或 ~/.bash_profile 文件： 在终端中打开 ~/.zshrc（如果你使用 Zsh）或 ~/.bash_profile（如果你使用 Bash），然后在 `Conda 初始化代码块之后` 添加以下内容， `/Users/iaa/bin/myenv` 根据实际情况更换为指定环境 ：

```
# 自动激活 myenv 环境
if [[ -z "$CONDA_DEFAULT_ENV" ]]; then
    conda activate /Users/iaa/bin/myenv
fi
```

这样，每次你打开一个新的终端时，如果没有激活的环境，它会自动激活 myenv 环境，而不是 base 环境。
完成这些步骤后，你的终端应该会自动激活 myenv 环境。
