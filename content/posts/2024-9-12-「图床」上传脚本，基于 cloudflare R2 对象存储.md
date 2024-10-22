---
title: "2024-9-12-「图床」上传脚本，基于 cloudflare R2 对象存储"
date: 2024-10-22T13:29:06+08:00
categories: ['Docs']
author: "Ronan"
---
使用之前需要通过 `pip install boto3` 安装 boto3 库。

使用方法：`usage: python imgs.py input_file`

在 `7-10` 行填入相应信息并将代码保存为 imgs.py：

- `account_id`：账户 id

  ![cloudflare_R2_account_id](https://imgs.ronan.us.kg/cloudflare_R2_account_id.png)
- `access_key_id`：s3客户端访问密钥
- `secret_access_key`：s3客户端机密访问密钥

  ![cloudflare_R2_S3_API_Token](https://imgs.ronan.us.kg/cloudflare_R2_S3_API_Token.png)
- `bucket_name`：R2 存储桶名称

  ![cloudflare_R2_bucket_name](https://imgs.ronan.us.kg/cloudflare_R2_bucket_name.png)

可以直接运行脚本或者将脚本[打包为应用程序](https://blog.ronan.us.kg/2024/09/02/python-%E6%89%93%E5%8C%85%E7%A8%8B%E5%BA%8F/)。

```python
import boto3
import os
from botocore.exceptions import ClientError
import argparse

# Cloudflare R2 配置
account_id = "" # 这里填入账户 id
access_key_id = "" # 这里填写 S3 客户端的‘访问密钥’
secret_access_key = "" #这里填写 S3 客户端的‘机密访问密钥’
bucket_name = "" # 这里填入你要上传文件到哪个 R2 存储桶的名称
endpoint_url = f"https://{account_id}.r2.cloudflarestorage.com"

# 创建 S3 客户端
s3 = boto3.client('s3',
                endpoint_url=endpoint_url,
                aws_access_key_id=access_key_id,
                aws_secret_access_key=secret_access_key)

def upload_file(file_path, object_name=None):
    """
    上传文件到 Cloudflare R2 存储桶

    :param file_path: 要上传的本地文件路径
    :param object_name: 在 R2 中的对象名称，如果不指定则使用文件名
    :return: 如果上传成功返回 True，否则返回 False
    """
    if object_name is None:
        object_name = os.path.basename(file_path)

    try:
        s3.upload_file(file_path, bucket_name, object_name)
        print(f"File {file_path} uploaded successfully as {object_name} \n\nhttps://imgs.ronan.us.kg/{object_name}")
        return True
    except ClientError as e:
        print(f"Error uploading file {file_path}: {e}")
        return False

def main():
    parser = argparse.ArgumentParser(description="传入本地或网络图片路径，即可上传到 cloudflare R2 对象存储桶")
    parser.add_argument("local_file_path", help="本地图片路径")

    args = parser.parse_args()

    upload_file(args.local_file_path)

if __name__=="__main__":
    main()
```
