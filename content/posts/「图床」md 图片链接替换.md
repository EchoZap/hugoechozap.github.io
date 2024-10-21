---
title: "「图床」md 图片链接替换"
date: 2024-10-21T21:55:54.022891
categories: ['Docs']
author: "Ronan"
---
在 main 函数的 old_domain 为旧的图片链接，new_domain 是新的图片链接，根据自身情况填写。

将以下代码保存为 `re.py` ,使用方法：`usage: re.py [-h] input_dir`，参数是一个目录路径。

```python
import os
import argparse
import re

class LinkReplace:
    def __init__(self, old_domain=None, new_domain=None, input_dir=None):
        self.old_domain = old_domain
        self.new_domain = new_domain
        self.input_dir = input_dir

        if not self.old_domain or not self.new_domain or not self.input_dir:
            raise ValueError("Both old_domain and new_domain must be provided")

    def replace_ibl_in_md(self): # 替换 md 文档的图片链接
        # 匹配 Markdown 文件中的图片链接的正则表达式
        image_pattern = re.compile(r'!\[.*?\]\((.*?)\)')

        for file_name in os.listdir(self.input_dir):
            # 拼接完整的文件路径
            file_path = os.path.join(self.input_dir, file_name)

            if file_path.endswith('md'):
                # 打开并读取文件内容
                with open(file_path, 'r', encoding='utf-8') as f:
                    content = f.read()

                # 查找符合条件的图片链接
                links = image_pattern.findall(content)

                modified = False

                # 遍历找到的链接
                for link in links:
                    if self.old_domain in link:
                        # 只替换包含 old_domain 的链接
                        updated_link = link.replace(self.old_domain, self.new_domain)
                        content = content.replace(link, updated_link)
                        modified = True

                    # 如果内容有更改，则写回文件
                    if modified:
                        with open(file_path, 'w', encoding='utf-8') as f:
                            f.write(content)
                        print(f"Updated links in: {file_path}")

    def get_ibl_in_md(self): # 输出 md 文档中所有的图片链接
        # 匹配 Markdown 文件中的图片链接的正则表达式
        image_pattern = re.compile(r'!\[.*?\]\((.*?)\)')

        for file_name in os.listdir(self.input_dir):
            # 拼接完整的文件路径
            file_path = os.path.join(self.input_dir, file_name)

            if file_path.endswith('md'):
                # 打开并读取文件内容
                with open(file_path, 'r', encoding='utf-8') as f:
                    content = f.read()

                # 查找符合条件的图片链接
                link = image_pattern.findall(content)

                if link:
                    print(f"{file_path}: {link}")

def main():

    parser = argparse.ArgumentParser(description="传入一个目录，替换目录下所有 md 文档的图片链接")
    parser.add_argument("input_dir", help="输入目录路径")

    args = parser.parse_args()

    # 要替换的域名
    old_domain = ""
    new_domain = ""

    lr = LinkReplace(old_domain, new_domain, args.input_dir)

    lr.replace_ibl_in_md()
    lr.get_ibl_in_md()


if __name__ == "__main__":
    main()
```
