---
title: "RonanBlog 备份自动化"
date: 2024-10-22T13:28:19+08:00
categories: ['Docs']
author: "Ronan"
---
> 本文仅适用于[Huxpro 博客及其模板](https://github.com/Huxpro/huxpro.github.io) ！！！

# 准备 `backup.py` 以及 `backup`

1.在 `仓库根目录` 下新建一个 backup 空目录，「为防止 github 自动忽略空目录，所以可以在backup 里面随便新建一个 t.md」  
2.将以下代码保存为 `backup.py` 并且放置到 `仓库根目录` 下

backup.py 源码：

```python
import os
import re
import argparse

class Backup:

    def __init__(self, source_path, backup):
        # 备份的文档路径
        self.backup = backup
        # 带日期前缀的博文目录
        self.source_path = source_path

    def process_file(self, file_name):
        # 去掉文件元数据和名称前面的日期

        removing_date_file = re.sub(r'^\d{4}-\d{2}-\d{2}-', '', file_name)

        # 读取文件内容并移除 YAML 前置事项
        with open(f"{self.source_path}/{file_name}", 'r', encoding='utf-8') as file:
            content = file.read()

        # 使用正则表达式找到并去掉第一个以“---”分隔的部分
        content = re.sub(r'^---.*?---\s*', '', content, flags=re.DOTALL)

        # 将修改后的内容写入新的文件
        with open(f"{self.backup}/{removing_date_file}", 'w', encoding='utf-8') as new_file:
            new_file.write(content)

    def delete_old_file(self):
        # 获取 backup 目录下的所有 md 文件
        backup_files = {f for f in os.listdir(self.backup) if f.endswith('.md')}

        # 获取 _posts 目录下的所有 md 文件
        source_files = {f for f in os.listdir(self.source_path) if f.endswith('.md')}

        # 获得_posts 目录下去除日期后的文件名的集合
        intermediate_name = {re.sub(r'^\d{4}-\d{2}-\d{2}-', '', f) for f in source_files}

        # 找出在 backup 目录中但不在 source 目录中的文件
        unmatched_files = backup_files - intermediate_name

        # 删除这些不一致的文件
        for file_name in unmatched_files:
            file_path = os.path.join(self.backup, file_name)
            os.remove(file_path)

    def get_post_name(self) -> list[str]:

        post_names = []
        for post_name in os.listdir(self.source_path):
            if post_name.endswith('.md') or post_name.endswith('.txt'):
                post_names.append(post_name)

        return post_names

def main():
    parser = argparse.ArgumentParser(description='Process a file to remove date from filename and YAML front matter.')

    # 添加一个位置参数来接受文件路径
    parser.add_argument('source_path', type=str, help='需要备份的目录')
    parser.add_argument('backup', type=str, help='备份文件存放的目录')

    # 解析命令行参数
    args = parser.parse_args()

    # 创建 Backup 类的实例
    backup = Backup(args.source_path, args.backup)
    post_names = backup.get_post_name()

    for post_name in post_names:
        backup.process_file(post_name)

    backup.delete_old_file()

    print("backup succeed")

if __name__ == '__main__':
    main()
```

# 修改actions
将仓库根目录下的 `.github/workflows/jekyll.yml` 内容修改为：

```yml

# This workflow uses actions that are not certified by GitHub.
# They are provided by a third-party and are governed by
# separate terms of service, privacy policy, and support
# documentation.

# Sample workflow for building and deploying a Jekyll site to GitHub Pages
name: Deploy Jekyll site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches: ["main"]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: write
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4



      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: 3.12

      - name: Install dependencies
        run: |
          pip install --upgrade pip

      - name: Run backup script
        run: |
          python backup.py _posts backup

      - name: Commit and push backup files
        run: |
          git config --local user.email "action@github.com"
          git config --local user.name "GitHub Action"
          git add .
          git commit -m "Updates backup files" || echo "No changes to commit"
          git push



      - name: Setup Ruby
        uses: ruby/setup-ruby@8575951200e472d5f2d95c625da0c7bec8217c42 # v1.161.0
        with:
          ruby-version: '3.1' # Not needed with a .ruby-version file
          bundler-cache: true # runs 'bundle install' and caches installed gems automatically
          cache-version: 0 # Increment this number if you need to re-download cached gems

      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5

      - name: Build with Jekyll
        # Outputs to the './_site' directory by default
        run: bundle exec jekyll build --baseurl "${{ steps.pages.outputs.base_path }}"
        env:
          JEKYLL_ENV: production

      - name: Upload artifact
        # Automatically uploads an artifact from the './_site' directory by default
        uses: actions/upload-pages-artifact@v3

  # Deployment job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```


#### actions改动的位置

`.github/workflows/jekyll.yml` 改动的位置是：

1. `permissions` 里的 contents 设置为 write
2. 在 biuld 工作流里添加了关于 backup.py 的使用

```yml
# This workflow uses actions that are not certified by GitHub.
# They are provided by a third-party and are governed by
# separate terms of service, privacy policy, and support
# documentation.

# Sample workflow for building and deploying a Jekyll site to GitHub Pages
name: Deploy Jekyll site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches: ["main"]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Ruby
        uses: ruby/setup-ruby@8575951200e472d5f2d95c625da0c7bec8217c42 # v1.161.0
        with:
          ruby-version: '3.1' # Not needed with a .ruby-version file
          bundler-cache: true # runs 'bundle install' and caches installed gems automatically
          cache-version: 0 # Increment this number if you need to re-download cached gems

      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5

      - name: Build with Jekyll
        # Outputs to the './_site' directory by default
        run: bundle exec jekyll build --baseurl "${{ steps.pages.outputs.base_path }}"
        env:
          JEKYLL_ENV: production

      - name: Upload artifact
        # Automatically uploads an artifact from the './_site' directory by default
        uses: actions/upload-pages-artifact@v3

  # Deployment job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```
