---
title: "Typora图床自动上传脚本"
date: 2024-10-21T21:53:37.757338
categories: ['Tools']
author: "Ronan"
---
> [!caution]
> typroa的图片上传脚本，针对[Telegraph-Image](https://github.com/cf-pages/Telegraph-Image)项目，适用于macOS和Linux系统。

以下准备了两种方法，分别是python脚本以及bash脚本，由于Windows通常对于bash没有很好的支持，而macOS以及Linux常常内置了完整的bash，所以大家根据自己实际情况选用合适自己的方式：

# 对于Bash脚本
## 使用前准备
在使用bash脚本之前需要安装jq，*jq是一个命令行json处理器，网络上有大量资料，这里不再赘述* ，以下是安装方法：

- macOS

```
brew install jq
```

- Linux：

```
# Debian/Ubuntu
apt install jq -y
```

## 脚本配置

> [!caution]
> **注意**：网址url后不需要加 / ，因为这可能会报错。就比如我的图床网址是https://wpb.pages.dev，但是在复制时浏览器总会添加https://wpb.pages.dev/，最后的/一定不能要！！！

在桌面(可随意指定自己想要放置的目录)新建一个 ui.sh 文件，将以下代码复制并保存到sh文件中，在sh代码中找到这一行并且在引号里填入自己的图床url：

```
# 自定义URL部分
base_url=""
```

- sh 脚本代码

```shell
#!/bin/bash

# 使用帮助信息
function display_help {
    echo "Usage: $0 [file1] [file2] ... [fileN]"
    echo
    echo "This script uploads images to a specified server and returns their URLs."
    echo
    echo "Options:"
    echo "  --help    Display this help message and exit"
    echo
    echo "Example:"
    echo "  $0 image1.jpg image2.png"
}

# 检查是否需要显示帮助信息
if [[ "$1" == "--help" ]]; then
    display_help
    exit 0
fi

# 自定义URL部分
base_url=""

# 检查是否安装了jq
if ! command -v jq &> /dev/null; then
    echo "Error: jq is not installed. Please install jq before running this script. "
    echo "You can install jq using the following command:"
    echo "brew install jq [macOS]"
    exit 1
fi

# 用于存储图片URL的数组
image_urls=()

# 循环读取参数
for file_path in "$@"; do
    # 发送上传图片请求，关闭curl输出
    response=$(curl --location --request POST "${base_url}/upload" \
        --header 'User-Agent: Apifox/1.0.0 (https://apifox.com)' \
        --form "file=@\"${file_path}\"" \
        --silent)  # 添加 --silent 选项以关闭输出

    # 检查请求是否成功
    if [ $? -eq 0 ]; then
        # 解析返回的JSON并拼接图片URL
        img_url="${base_url}$(echo "$response" | jq -r '.[0].src')"

        # 存储图片URL到数组
        image_urls+=("${img_url}")
    else
        # 请求失败，输出错误信息并退出脚本
        echo "Upload Failed"
        exit 1
    fi
done

# 所有请求成功后输出成功信息
echo "Upload Success"

# 输出所有图片URL
for url in "${image_urls[@]}"; do
    echo "${url}"
done
```       


## Typroa配置

记住脚本的位置，如:

```
～/project/scripts/typora-uploader/ui.sh
```

进入Typroa`设置`->`图像`->`上传服务设定`，将上传服务改为`自定义命令`，命令为`脚本路径`：

![img](https://imgs.ronan.us.kg/typora_upload.png)

记得在插入图片时选择`上传图片`，并勾选`对本地位置的图片应用上述规则`。

---

# 对于python脚本
## 使用前准备
需要安装 `requests` 和 `fire` 模块，使用以下命令安装：

```
pip3 install requests
```

```
pip3 install fire
```

## 脚本配置

> [!caution]
> **注意**：网址url后不需要加 / ，因为这可能会报错。就比如我的图床网址是https://wpb.pages.dev，但是在复制时浏览器总会添加https://wpb.pages.dev/，最后的/一定不能要！！！

在桌面(可随意指定自己想要放置的目录)新建一个 ui.py 文件，将以下代码复制并保存到py文件中，在main函数里找到这一行并且在引号里填入自己的图床url：

```
# 在这里输入个人图床地址
base_url = ''
```

- py 脚本代码

```python
import sys
import requests
import json
import fire

def display_help():
    help_message = """
用法: python script.py [file1] [file2] ... [fileN]

此脚本将图像上传到指定的服务器并返回其URL。

示例:
    python3 script.py image1.jpg image2.png
"""
    print(help_message)

def upload(base_url, img_path):

    upload_url = f"{base_url}/upload"
    header = {"User-Agent": "Apifox/1.0.0 (https://apifox.com)"}
    with open(img_path, 'rb') as file:
        files = {
            'file': file  
        }
        try:
            repo = requests.post(url=upload_url, headers=header, files=files)
        except Exception as e:
            print(f"Failed to upload file: {repo.status_code}")
        else:
            img_url = f'{base_url}{repo.json()[0]["src"]}'
            return img_url

def main(*img_paths):
    # 如果没有参数
    if len(sys.argv) == 1 :
        display_help()
        sys.exit(0)

    # 在这里输入个人图床地址
    base_url = ''
    
    for img_path in img_paths:
        img_url = upload(base_url, img_path)
        if img_url:
            print(f"Image URL: {img_url}")

if __name__ == "__main__":
    fire.Fire(main)
```     


## Typroa配置
在配置typora之前，还需要对py脚本进行以下处理:

```python
pyinstaller --onefile ui.py
```

将 ui.py 替换为你要打包的脚本文件名(在本例中使用的是ui), 打包完成后，PyInstaller 会在当前目录下生成一个 dist 文件夹，其中包含你的可执行文件。你可以将这个文件复制到其他没有 Python 环境的系统上运行。  
如果 pyinstall 未安装，具体用法以及安装请查看 [pyinstaller](https://blog.ronan.us.kg/post/%E3%80%8Cpython%E3%80%8D-da-bao-cheng-xu.html)

如果以上步骤顺利进行，那么应该可以在dist目录中找到名为ui的可执行程序，然后复制该程序的路径。假设在以下路径，将其按教程填入typora设置中即可配置完成:

```
～/project/scripts/typora-uploader/ui
```

进入Typroa`设置`->`图像`->`上传服务设定`，将上传服务改为`自定义命令`，命令为`脚本路径`：

![img](https://imgs.ronan.us.kg/typora_upload.png)

记得在插入图片时选择`上传图片`，并勾选`对本地位置的图片应用上述规则`。

