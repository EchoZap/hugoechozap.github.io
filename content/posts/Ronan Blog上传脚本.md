---
title: "Ronan Blog上传脚本"
date: 2024-10-21T21:53:40.597808
categories: ['Tools']
author: "Ronan"
---
# 用法

- 在代码的最后几行空白引号里填入相应信息
- **注意** `create_new_file_in_the_repo` 函数里的 `branch="main"` 参数，根据自身仓库分支修改


```python
import os
from datetime import datetime
from github import Github

class HuxBlog:
    def __init__(self, owner=None, repo=None, token=None):
        self.owner = owner
        self.repo = repo
        self.token = token

        g = Github(self.token)
        self.repo = g.get_repo(f"{self.owner}/{self.repo}")

        # 检查是否提供了必要的参数
        if not self.owner or not self.repo or not self.token:
            raise ValueError("必须指定 owner, repo 和 token")
        else:
            self.start_upload()


    def start_upload(self):
        status = input("\n 1.上传单篇文章 \n 2.批量上传 \n 按下对应数字并回车可选择相应功能：")
        self.tags = input("请输入标签，用,隔开：")

        match status:
            case "1":
                post_path = input("请输入文章路径：")
                including_time_file_name, content = self.prepare_post_file(post_path)
                self.create_new_file_in_the_repo(including_time_file_name, content)
            case "2":
                post_paths = self.get_post_paths()
                for post_path in post_paths:
                    including_time_file_name, content = self.prepare_post_file(post_path)
                    self.create_new_file_in_the_repo(including_time_file_name, content)


    def create_new_file_in_the_repo(self, file_path, content):
        # 第一个参数：要上传到仓库的哪个路径; 第二个参数：commit 信息; 第三个参数：上传文档正文; 第四个参数：上传的分支
        self.repo.create_file(f"{file_path}", f"Added {file_path}", content, branch="main")

    def get_post_paths(self):
        directory = input("请输入文章目录：")

        file_paths = []

        for filename in os.listdir(directory):
            if filename.endswith('.md') or filename.endswith('.txt'):
                # 包含前缀路径的文件路径
                file_path = os.path.join(directory, filename)
                file_paths.append(file_path)

        return file_paths


    def prepare_post_file(self, post_path:str) -> tuple[_posts/YYYY-MM-DD-file.md:str, front_matter + source_body:str]:

        # 获取当前日期并格式化为“YYYY-MM-DD-”
        current_date = datetime.now().strftime('%Y-%m-%d')

        title = os.path.splitext(os.path.basename(post_path))[0]

        # 检查文件名是否已经以“YYYY-MM-DD-”格式开头
        if title.startswith(current_date):
            print(f"{post_path}的文件名已包含日期，如需继续上传请先清除文件名前的日期")
        else:
            # file.md 所在的目录 “/root/path”
            dir_name, base_name = os.path.split(post_path)
            # 无后缀名的 file
            file_name, file_ext = os.path.splitext(base_name)

            # 构建 YYYY-MM-DD-file.md ,没有前置路径
            over_time_name = f"_posts/{current_date}-{file_name}{file_ext}"
            # 构建/root/path/YYYY-MM-DD-file.md
            absolute_path_file = os.path.join(dir_name, over_time_name)


        tag_list = [tag.strip() for tag in self.tags.split(',')]

        # 生成前言部分
        front_matter = f"""---
layout: post
title: "{title}"
author: "Ronan"
header-style: text
tags:
"""
        for tag in tag_list:
            front_matter += f"  - {tag}\n"

        front_matter += "---\n\n"

        # 读取源文件内容
        with open(post_path, "r", encoding='utf-8') as f:
            source_body = f.read()

        # 构建要写入的内容
        meta_content = front_matter + source_body

        return over_time_name, meta_content

if __name__ == "__main__":
    blog = HuxBlog(
        owner = "",
        repo = "",
        token = ""
    )

```



<!-- ##{"timestamp":1722140467}## -->
