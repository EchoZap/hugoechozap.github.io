---
title: "「图床」上传脚本，基于 GitHub 仓库"
date: 2024-10-21T21:56:09.814178
categories: ['Docs']
author: "Ronan"
---
使用之前需要通过 `pip install PyGithub` 安装 github 库

在 `35-37` 行填入相应信息并将代码保存为 imgs.py：
- `owner`:github 用户名
- `repo`:仓库名(如 imgs)
- `token`:github 私人访问令牌(要给予仓库读写权限)

可以通过以下方法直接运行脚本或者将脚本[打包为应用程序](https://blog.ronan.us.kg/2024/09/02/python-%E6%89%93%E5%8C%85%E7%A8%8B%E5%BA%8F/)

使用方法：`usage: python imgs.py [-h] input_file [input_file ...]`

```python
from github import Github
import os
import argparse
import base64

class Imgs:
    def __init__(self, owner=None, repo=None, token=None):
        self.owner = owner
        self.repo = repo
        self.token = token

        g = Github(self.token)
        self.repo = g.get_repo(f"{self.owner}/{self.repo}")

    def create_new_file(self, img, content):
        # 第一个参数：要上传到仓库的哪个路径; 第二个参数：commit 信息; 第三个参数：上传文档正文; 第四个参数：上传的分支
        self.repo.create_file(f"blog_imgs/{img}", f"Newfiles: {img} ", content, branch="main")

    def get_img_content(self, img_path):
        with open(img_path, "rb") as image_file:
            img_content = image_file.read()

        return img_content


def main():
    parser = argparse.ArgumentParser(description="基于 echozap/imgs 的图床上传")

    # 传递的图片数量不确定
    parser.add_argument('input_file', type=str, nargs='+', help='输入图片的路径')

    args = parser.parse_args()

    img = Imgs(
        owner = "",
        repo = "",
        token = ""
    )

    for img_path in args.input_file:
        try:
            img_content = img.get_img_content(img_path)
            img_name = os.path.basename(img_path) # 获取带扩展名的文件名

            img.create_new_file(img_name, img_content)

            print(f"{img_name}上传成功")
            print(f"https://img.ronan.us.kg/blog_imgs/{img_name}")
        except Exception as e:
            if '"status": "422"' in str(e):
                print(f"上传 {img_path} 时发生错误: {e}")
                print("图片已存在")
                print(f"https://img.ronan.us.kg/blog_imgs/{img_name}")

if __name__ == "__main__":
    main()
```
