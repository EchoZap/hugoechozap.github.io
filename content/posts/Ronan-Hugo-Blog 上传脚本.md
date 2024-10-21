---
title: "Ronan-Hugo-Blog 上传脚本"
date: 2024-10-21T21:43:38.857958
categories: ['Docs']
author: "Ronan"
---
使用方法：

在76-78行填入相应信息即可

```python
import os
from datetime import datetime
from github import Github

class HugoBlog:
    def __init__(self, owner=None, repo=None, token=None):
        self.owner = owner
        self.repo = repo
        self.token = token

        g = Github(self.token)
        self.repo = g.get_repo(f"{self.owner}/{self.repo}")

        # 检查是否提供了必要的参数
        if not self.owner or not self.repo or not self.token:
            raise ValueError("必须指定 owner, repo 和 token")

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

    def generate_md_front_matter(self, post_path, categories_input, author="Ronan"):
        # 获取不带前缀路径和后缀名的文件名
        title = os.path.splitext(os.path.basename(post_path))[0]

        # 获取当前实时时间，格式化为指定格式 date: 2024-10-21T21:10:51.719748
        current_time = datetime.now().isoformat()

        # 处理用户输入的 categories
        categories = [cat.strip().capitalize() for cat in categories_input.split(',')]

        # 生成 MD 头部内容
        front_matter = f"""---
title: "{title}"
date: {current_time}
categories: {categories}
author: "{author}"
---
"""

        return front_matter

    def append_front_matter_to_md_file(self, front_matter, post_path:str):
        # file.md 所在的目录 “/root/path”
        dir_name, base_name = os.path.split(post_path)
        # 无后缀名的 file
        file_name, file_ext = os.path.splitext(base_name)

        final_path = f"content/posts/{base_name}"

        # 读取源文件内容
        with open(post_path, "r", encoding='utf-8') as f:
            source_body = f.read()

        # 将前言追加到 md 文档中
        final_content = front_matter + source_body

        return final_content, final_path

def main():

    blog = HugoBlog(
        owner = " ",
        repo = "",
        token = ""
    )

    status = input("\n 1.上传单篇文章 \n 2.批量上传 \n 按下对应数字并回车可选择相应功能：")
    categories_input = input("请输入标签，用,隔开：")

    match status:
        case "1":
            post_path = input("请输入文章路径：")
            front_matter = blog.generate_md_front_matter(post_path, categories_input)
            final_content, final_path = blog.append_front_matter_to_md_file(front_matter, post_path)

            blog.create_new_file_in_the_repo(final_path, final_content)

        case "2":
            post_paths = blog.get_post_paths()
            for post_path in post_paths:
                front_matter = blog.generate_md_front_matter(post_path, categories_input)
                final_content, final_path = blog.append_front_matter_to_md_file(front_matter, post_path)

                blog.create_new_file_in_the_repo(final_path, final_content)


if __name__ == "__main__":
    main()

```
