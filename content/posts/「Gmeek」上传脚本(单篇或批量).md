---
title: "「Gmeek」上传脚本(单篇或批量)"
date: 2024-10-21T21:56:58.538750
categories: ['Docs']
author: "Ronan"
---
> [!caution]
>
> **注意：** 该脚本仅适用于通过Gmeek项目搭建的博客网站
> 在开始使用之前，需要创建 github 的个人 Token


# 1.创建github个人Token

1.在 GitHub 上任何页面的**右上角**，单击您的`个人资料照片`，然后单击 `Settings`。  
2.在左侧边栏中，单击 `Developer settings（开发人员设置）`。  
3.在左侧边栏中的 **Personal access tokens （个人访问令牌）** 下，单击 `Tokens （classic）`。  
4.选择 `Generate new token（生成新令牌）`，然后单击 `Generate new token （classic）`。  
5.在 **Note** 字段中，为您的令牌指定一个描述性名称。  
6.要为您的令牌指定过期时间，请选择 **Expiration（过期）**，然后选择默认选项或单击 Custom（自定义）以输入日期。  
7.选择要授予此令牌的范围。要使用令牌从命令行访问存储库，请选择 `repo` ( **这里一定要打上勾！！！** ) 。没有分配范围的令牌只能访问公共信息。  
8.滑动到底部，单击 `Generate token`  
9.**将新令牌复制到剪贴板并且保管好，令牌只出现一次，一定要保管好！一定要保管好！一定要保管好！**。  


# 2.使用方法
以下有两个方法，分为sh脚本和python脚本：

> [!tip]
> **建议：**
> Linux与mac系统使用 sh 脚本，Windows使用py脚本体验更佳
> 也可根据喜好自行采用

## 2.1配置脚本

### 对于 sh 脚本
> [!important]
>
> 使用前需确认以下：
> - 系统已安装curl
> - 系统已安装jq
> - 系统可使用bash

curl、jq 安装方法由于系统不同，方法多样，所以具体安装方法可以自行查找...

在代码开始的前几行，找到以下几个值，在引号里填入自己的信息：

- `TOKEN`  为自己创建的Token值
- `OWNER` 为自己的github用户名
- `REPO` 为自己的Gmeek博客仓库名，一般是 `xxx.github.io`
  
  
### 对于 py 脚本

1.在开始使用之前，需要安装 `requests` 模块

```shell
pip3 install requests
```

2.找到代码最后几行，在引号里面填入：

- `token` 为自己创建的Token值
- `owner` 为自己的github用户名
- `repo` 为自己的Gmeek博客仓库名，一般是 `xxx.github.io`

- sh 脚本代码

```shell
#!/usr/bin/env bash

TOKEN=''
OWNER=''
REPO=''

# 上传主程序
main_upload_program() {
    local title=$1
    local content=$2
    local labels=$3

    # 将标签转换成 JSON 数组格式
    labels_json=$(echo "$labels" | sed 's/,/","/g' | sed 's/^/["/' | sed 's/$/"]/')

    # 构建 JSON 数据
    json_data=$(jq -n \
    --arg title "$title" \
    --arg body "$content" \
    --argjson labels "$labels_json" \
    '{title: $title, body: $body, labels: $labels}')

    # 发送 POST 请求
    status_code=$(    curl -L \
        --silent \
        --output /dev/null \
        --write-out "%{http_code}" \
        -X POST \
        -H "Accept: application/vnd.github+json" \
        -H "Authorization: Bearer $TOKEN" \
        -H "X-GitHub-Api-Version: 2022-11-28" \
        https://api.github.com/repos/$OWNER/$REPO/issues \
        -d "$json_data" )

    case $status_code in
        201)
            echo
            echo "返回码：$status_code"
            echo "上传成功"
        ;;
        400)
            echo
            echo "返回码：$status_code"
            echo "Bad Request, 错误请求"
        ;;
        403)
            echo
            echo "返回码：$status_code"
            echo "Forbidden"
        ;;
        404)
            echo
            echo "返回码：$status_code"
            echo "Resource not found"
        ;;
        410)
            echo
            echo "返回码：$status_code"
            echo "Gone"
        ;;
        422)
            echo
            echo "返回码：$status_code"
            echo "Validation failed, or the endpoint has been spammed. \n 验证失败，或终结点已收到垃圾邮件。"
        ;;
        502)
            echo
            echo "返回码：$status_code"
            echo "Service unavailable, 服务不可用"
        ;;
    esac

}

# 上传单篇文章
upload_single_post() {
    read -p "请输入文章路径：" file_path
    read -p "请输入文章标签(多个标签请用,隔开)：" labels

    if echo "$file_path" | grep -E '\.(md|txt)$' > /dev/null; then
        # 提取文件名并去掉扩展名
        file_name=$(basename "$file_path")
        title=$(echo "$file_name" | sed 's/\.[^.]*$//')

        # 获取文章内容
        content=$(sed "" "$file_path")

        main_upload_program "$title" "$content" "$labels"
    fi

}

# 批量上传
upload_batch_posts() {
    read -p "请输入要上传的文件目录(绝对、相对路径皆可)：" file_path
    read -p "请输入文章标签(多个标签请用,隔开)：" labels
    for file in "${file_path}"/*
    do
        if echo "$file" | grep -E '\.(md|txt)$' > /dev/null; then
            # 提取文件名并去掉扩展名
            file_name=$(basename "$file")
            title=$(echo "$file_name" | sed 's/\.[^.]*$//')

            # 获取文章内容
            content=$(sed "" "$file")

            main_upload_program "$title" "$content" "$labels"
        fi

    done
}


while true;do
    echo " -----Made by Ronan----- "
    echo " 在键盘上按下对应数字即可选择相应设置"
    echo "————————————————————————————————————————————————————"
    echo " 1. 上传单篇文章"
    echo " 2. 批量上传"
    echo " Q. 退出本程序"
    echo
    read -p "请选择一个选项: " status
    case $status in
        1)
            upload_single_post
            break
        ;;
        2)
            upload_batch_posts
            break
        ;;
        q | Q)
            echo "退出"
            exit 0
        ;;
        *)
            echo "无效选项，请重新选择。"
        ;;
    esac
done
```

- py 脚本代码

```python
import json
import os
import requests

class GitHubIssueUploader:
    def __init__(self, owner=None, repo=None, token=None):
        self.owner = owner
        self.repo = repo
        self.token = token

        # 检查是否提供了必要的参数
        if not self.owner or not self.repo or not self.token:
            raise ValueError("必须指定 owner, repo 和 token")
        else:
            self.start_upload()

    def create_issue(self, title, content, labels):

        url = f"https://api.github.com/repos/{self.owner}/{self.repo}/issues"

        headers = {
            "Accept": "application/vnd.github+json",
            "Authorization": f"Bearer {self.token}",
            "X-GitHub-Api-Version": "2022-11-28"
        }

        data = {
            "title": title,
            "body": content,
            "labels": labels
        }

        response = requests.post(url=url, headers=headers, json=data)

        match response.status_code:
            case 201:
                print("Created, 上传成功")
            case 400:
                print("Bad Request, 错误请求")
            case 403:
                print("Forbidden")
            case 404:
                print("Resource not found")
            case 410:
                print("Gone")
            case 422:
                print("Validation failed, or the endpoint has been spammed. \n 验证失败，或终结点已收到垃圾邮件。")
            case 502:
                print("Service unavailable, 服务不可用")

    def upload_single_post(self, file_path, labels):
        title = os.path.splitext(os.path.basename(file_path))[0]
        with open(file_path, 'r', encoding='utf-8') as file:
            content = file.read()
            self.create_issue(title, content, labels)

    def upload_batch_posts(self, directory, labels):
        for filename in os.listdir(directory):
            if filename.endswith('.md') or filename.endswith('.txt'):
                file_path = os.path.join(directory, filename)
                self.upload_single_post(file_path, labels)

    def start_upload(self):
        status = input("\n 1.上传单篇文章 \n 2.批量上传 \n 按下对应数字并回车可选择相应功能：")
        labels = input("请输入标签，用,隔开：")
        labels = [word.strip() for word in labels.split(',')]

        if status == "1":
            file_path = input("请输入文件路径：")
            self.upload_single_post(file_path, labels)
        elif status == "2":
            directory = input("请输入目录路径：")
            self.upload_batch_posts(directory, labels)
        else:
            print("已退出程序...")
            exit()


if __name__ == "__main__":
    uploader = GitHubIssueUploader(
        owner = "xxxx",
        repo = "xxxx.github.io",
        token = "xxxx"
    )
```


## 2.2运行脚本

- 对于sh脚本

将以上脚本代码保存为 `blog_upload.sh` ，在终端里进入到改脚本所在位置，键入 `chmod 777 blog_upload.sh` ，然后通过 `./blog_upload.sh` 运行，之后根据提示进行即可。


- 对于py脚本

将脚本代码保存为 `blog_upload.py` ，在终端里进入到改脚本所在位置，然后通过以下命令运行，之后根据提示进行即可。

```python
python3 blog_upload.py
```

你问我配置完了脚本怎么用？你运行起来有步骤引导你的～～～

<!-- ##{"timestamp":1722611382}## -->
