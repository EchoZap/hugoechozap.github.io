---
title: "Dockerfile的简单实现"
date: 2024-10-21T21:55:55.954014
categories: ['Docs']
author: "Ronan"
---
# 构建第一个Dockerfile

假设该镜像实现的等同于我们在已经配置好python环境的机器上通过`python hello.py`命令来运行一个python脚本

所以该Dockerfile的构建有以下步骤：

1. 在桌面或其他位置新建一个文件夹，假设文件夹名为docker
2. 在docker新建一个hello.py文件，hello.py已经实现所需功能
3. 再在docker新建一个`Dockerfile`，**注意：仅开头且必须大写**

以下是Dockerfile内容：

```shell
FROM python:3.9.19-alpine3.18
COPY hello.py /hello.py
CMD python hello.py
```

# Dockerfile语法说明

`Dockerfile` 是 Docker 构建镜像的描述文件，它包含了一系列指令，描述了如何从基础镜像创建一个新的 Docker 镜像。

下面是 `Dockerfile` 中常用指令的说明及其语法，参考自Docker Dockerfile | 菜鸟教程 (runoob.com)：


| `Dockerfile` 指令 | 说明                                                                 |
| ----------------- | -------------------------------------------------------------------- |
| FROM              | 指定基础镜像，用于后续的指令构建。                                   |
| MAINTAINER        | 指定Dockerfile的作者/维护者。（已弃用，推荐使用LABEL指令）           |
| LABEL             | 添加镜像的元数据，使用键值对的形式。                                 |
| RUN               | 在构建过程中在镜像中执行命令。                                       |
| CMD               | 指定容器创建时的默认命令。（可以被覆盖）                             |
| ENTRYPOINT        | 设置容器创建时的主要命令。（不可被覆盖）                             |
| EXPOSE            | 声明容器运行时监听的特定网络端口。                                   |
| ENV               | 在容器内部设置环境变量。                                             |
| ADD               | 将文件、目录或远程URL复制到镜像中。                                  |
| COPY              | 将文件或目录复制到镜像中。                                           |
| VOLUME            | 为容器创建挂载点或声明卷。                                           |
| WORKDIR           | 设置后续指令的工作目录。                                             |
| USER              | 指定后续指令的用户上下文。                                           |
| ARG               | 定义在构建过程中传递给构建器的变量，可使用 "docker build" 命令设置。 |
| ONBUILD           | 当该镜像被用作另一个构建过程的基础时，添加触发器。                   |
| STOPSIGNAL        | 设置发送给容器以退出的系统调用信号。                                 |
| HEALTHCHECK       | 定义周期性检查容器健康状态的命令。                                   |
| SHELL             | 覆盖Docker中默认的shell，用于RUN、CMD和ENTRYPOINT指令。              |

详细说明：

#### 1. `FROM`

指定构建镜像所基于的基础镜像。每个 Dockerfile 必须以 `FROM` 指令开始。

FROM是必须的，这是搭建镜像的基础

* `python:3.9.19-alpine3.18`前面的`python`为镜像名称，可到dockerhub按需搜索，如下图

![img](https://imgs.ronan.us.kg/dockerfile1.png)

* `python:3.9.19-alpine3.18`冒号后面的是tag，也就是标签(版本号)。**标签必须与dockerhub镜像里提供的一致，在上图点击所需镜像名称即可看到可用标签。**

![img](https://imgs.ronan.us.kg/dockerfile2.png)

```
FROM ubuntu:20.04
```

#### 2. `LABEL`

LABEL 指令用来给镜像添加一些元数据（metadata），以键值对的形式，语法格式如下：

```
LABEL <key>=<value> <key>=<value> <key>=<value> ...
```

例如添加镜像作者信息：

```
LABEL maintainer="your-email@example.com"
```

#### 3. `ENV`

设置环境变量，这些环境变量在构建和运行容器时都会存在。

格式如下：

```
ENV <key> <value>
ENV <key1>=<value1> <key2>=<value2>...
```

以下示例设置 NODE\_VERSION = 7.2.0 ， 在后续的指令中可以通过 \$NODE\_VERSION 引用：

```
ENV NODE_VERSION 7.2.0

RUN curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.xz" \
  && curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt.asc"
```

#### 4. `RUN`

执行命令行指令。这些指令在构建镜像时执行，通常用于安装软件包、修改文件等操作。

RUN命令有俩种格式：

1）shell 格式：

```
RUN <命令行命令>
# <命令行命令> 等同于，在终端操作的 shell 命令。
```

2）exec 格式：

```
RUN ["可执行文件", "参数1", "参数2"]
# 例如：
# RUN ["./test.php", "dev", "offline"] 等价于 RUN ./test.php dev offline
```

> **注意**：`Dockerfile` 的指令每执行一次都会在 docker 上新建一层。所以过多无意义的层，会造成镜像膨胀过大。尽量将命令行指令通过`&&`连接写为一条指令。

```
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    curl \
    vim \
    git \
    && rm -rf /var/lib/apt/lists/*
```

#### 5. `COPY`

复制指令，从上下文目录中复制文件或者目录到容器里指定路径。

格式：

```
COPY [--chown=<user>:<group>] <源路径1>...  <目标路径>
COPY [--chown=<user>:<group>] ["<源路径1>",...  "<目标路径>"]
```

**[--chown=:]**：可选参数，用户改变复制到容器内文件的拥有者和属组。

**<源路径>**：源文件或者源目录，这里可以是通配符表达式，其通配符规则要满足 Go 的 filepath.Match 规则。例如：

```
COPY hom* /mydir/
COPY hom?.txt /mydir/
```

**<目标路径>**：容器内的指定路径，该路径不用事先建好，路径不存在的话，会自动创建。

#### 6. `ADD`

ADD 指令和 COPY 的使用格类似（**同样需求下，官方推荐使用 COPY**）。功能也类似，不同之处如下：

* ADD 的优点：在执行 <源文件> 为 tar 压缩文件的话，压缩格式为 gzip, bzip2 以及 xz 的情况下，会自动复制并解压到 <目标路径>。
* ADD 的缺点：在不解压的前提下，无法复制 tar 压缩文件。会令镜像构建缓存失效，从而可能会令镜像构建变得比较缓慢。具体是否使用，可以根据是否需要自动解压来决定。

#### 7. `WORKDIR`

指定工作目录。用 WORKDIR 指定的工作目录，会在构建镜像的每一层中都存在。以后各层的当前目录就被改为指定的目录，如该目录不存在，WORKDIR 会帮你建立目录。

docker build 构建镜像过程中的，每一个 RUN 命令都是新建的一层。只有通过 WORKDIR 创建的目录才会一直存在。

格式：

```
WORKDIR <工作目录路径>
```

#### 8. `EXPOSE`

仅仅只是声明端口，此指令不会实际发布端口。

作用：

* 帮助镜像使用者理解这个镜像服务的守护端口，以方便配置映射。
* 在运行时使用随机端口映射时，也就是 `docker run -P` 时，会自动随机映射 EXPOSE 的端口。

格式：

```
EXPOSE <端口1> [<端口2>...]
```

#### 9. `CMD`

指定容器启动时默认执行的命令。`CMD` 指令指定的程序可被 docker run 命令行参数中指定要运行的程序所覆盖。

```
CMD ["nginx", "-g", "daemon off;"]
```

格式：

```
CMD <shell 命令> 
CMD ["<可执行文件或命令>","<param1>","<param2>",...] 
CMD ["<param1>","<param2>",...]  # 该写法是为 ENTRYPOINT 指令指定的程序提供默认参数
```

推荐使用第二种格式，执行过程比较明确。第一种格式实际上在运行的过程中也会自动转换成第二种格式运行，并且默认可执行文件是 sh。

**注意**：如果 `Dockerfile` 中如果存在多个 `CMD`指令，仅最后一个生效。

#### 10. `ENTRYPOINT`

类似于 `CMD`，但不能被 `docker run` 提供的参数覆盖，而且这些命令行参数会被当作参数送给 ENTRYPOINT 指令指定的程序。

如果运行 docker run 时使用了 `--entrypoint` 选项，将覆盖 `ENTRYPOINT` 指令指定的程序。通常用来设置不可变的部分，`CMD` 用来指定可变的参数。

格式：

```
ENTRYPOINT ["<executeable>","<param1>","<param2>",...]
```

搭配 CMD 命令使用：

```
ENTRYPOINT ["nginx"]
CMD ["-g", "daemon off;"]
```

**注意**：如果 `Dockerfile` 中如果存在多个 `ENTRYPOINT` 指令，仅最后一个生效。

示例：

假设已通过 `Dockerfile` 构建了 `nginx:test` 镜像：

```
FROM nginx

ENTRYPOINT ["nginx", "-c"] # 定参
CMD ["/etc/nginx/nginx.conf"] # 变参 
```

1、不传参运行

```
$ docker run  nginx:test
```

容器内会默认运行以下命令，启动主进程。

```
nginx -c /etc/nginx/nginx.conf
```

2、传参运行

```
$ docker run  nginx:test -c /etc/nginx/new.conf
```

容器内会默认运行以下命令，启动主进程(/etc/nginx/new.conf:假设容器内已有此文件)

```
nginx -c /etc/nginx/new.conf
```

#### 11. `VOLUME`

定义匿名数据卷。在启动容器时忘记挂载数据卷，会自动挂载到匿名卷。创建挂载点，声明了容器中的一个目录将作为数据卷。

作用：

* 避免重要的数据，因容器重启而丢失，这是非常致命的。
* 避免容器不断变大。

格式：

```
VOLUME ["<路径1>", "<路径2>"...]
VOLUME <路径>
```

在启动容器 docker run 的时候，我们可以通过 -v 参数修改挂载点。

#### 12. `USER`

用于指定执行后续命令的用户和用户组，这边只是切换后续命令执行的用户（用户和用户组必须提前已经存在）。

格式：

```
USER <用户名>[:<用户组>]
```

#### 13. `ARG`

构建参数，与 ENV 作用一致。不过作用域不一样。ARG 设置的环境变量仅对 Dockerfile 内有效，也就是说只有 docker build 的过程中有效，构建好的镜像内不存在此环境变量。

构建命令 docker build 中可以用 --build-arg <参数名>=<值> 来覆盖。

格式：

```
ARG <参数名>[=<默认值>]
```

示例：

```
ARG VERSION=1.0
RUN echo "Version: $VERSION"
```

#### 14. `HEALTHCHECK`

用于指定某个程序或者指令来监控 docker 容器服务的运行状态。

格式：

```
HEALTHCHECK [选项] CMD <命令>：设置检查容器健康状况的命令
HEALTHCHECK NONE：如果基础镜像有健康检查指令，使用这行可以屏蔽掉其健康检查指令

HEALTHCHECK [选项] CMD <命令> : 这边 CMD 后面跟随的命令使用，可以参考 CMD 的用法。
```

#### 15. `ONBUILD`

用于延迟构建命令的执行。简单的说，就是 Dockerfile 里用 ONBUILD 指定的命令，在本次构建镜像的过程中不会执行（假设镜像为 test-build）。当有新的 `Dockerfile` 使用了之前构建的镜像 FROM test-build ，这时执行新镜像的 `Dockerfile` 构建时候，会执行 test-build 的 Dockerfile 里的 ONBUILD 指定的命令。

格式：

```
ONBUILD <其它指令>
```



# 构建镜像实战

### 1. 创建 `Dockerfile`

`Dockerfile` 是一个包含构建镜像指令的文本文件。以下是一个示例 `Dockerfile`，它构建了一个基于 Ubuntu 的基础镜像，并安装了一些常用的工具。****

1. 创建一个名为 `Dockerfile` 的文件。

```
# 使用官方的 Python 作为基础镜像
FROM python:3.10-slim

# 设置工作目录
WORKDIR /app

# 复制本地的 requirements.txt 文件到镜像中
COPY requirements.txt requirements.txt

# 安装依赖包
RUN pip install --no-cache-dir -r requirements.txt

# 复制当前目录内容到镜像中的 /app 目录
COPY . .

# 暴露应用运行的端口
EXPOSE 5000

# 设置环境变量
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0

# 运行 Flask 应用
CMD ["flask", "run"]
```

目录结构：

```
my_flask_app/
│
├── Dockerfile
├── app.py
└── requirements.txt
```

requirements.txt文件内容：

```
Flask
```

Flask 应用程序文件 `app.py`：

```
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, World!'

if __name__ == '__main__':
    app.run(host='0.0.0.0')
```

### 2. 构建 Docker 镜像

1. 在 Dockerfile 所在目录下，运行以下命令构建 Docker 镜像：

```
docker build -t myflaskapp:1.0 .
```

以上命令将根据 `Dockerfile` 的内容构建镜像，并将其标记为 `mybaseimage:1.0`。

### 3. 验证及管理镜像

1. 构建完成后，您可以使用以下命令查看已创建的镜像：

```
docker images
```

您应该会看到类似以下的输出：

```
REPOSITORY       TAG          IMAGE ID       CREATED          SIZE
myflaskapp       1.0          a405b0aca5f0   10 minutes ago   133MB
```

2. 查看镜像的各个层

查看镜像的各个层，请使用 docker history 命令

```
docker history myflaskapp:1.0
# 或者
docker inspect myflaskapp:1.0
```

3. 可以使用以下命令运行新创建的镜像，以确保其工作正常：

```
[root@k8s-master my_flask_app]# docker run -d -p 5000:5000 myflaskapp:1.0
426420bedd2754508643d5cea5430911c4900809e608b649470e3989b53ed0f1
[root@k8s-master my_flask_app]# ss -tulnp | grep 5000
tcp    LISTEN     0      128       *:5000                  *:*                   users:(("docker-proxy",pid=55916,fd=4))
tcp    LISTEN     0      128    [::]:5000               [::]:*                   users:(("docker-proxy",pid=55922,fd=4))
[root@k8s-master my_flask_app]# curl 127.0.0.1:5000
Hello, World!
```

4. 删除镜像(按需)

```
docker image rm myflaskapp:1.0
```

### 4. 推送镜像到 Docker 仓库（可选）

如果您希望将镜像分享给其他人或在不同的环境中使用，可以将其推送到 Docker Hub 或其他 Docker 镜像仓库。

1. 登录到 Docker Hub：

```
docker login
```

2. 给镜像打标签：

```
docker tag my-images:1.0 your-dockerhub-username/my-images:1.0
```

3. 推送镜像到 Docker Hub：

```
docker push your-dockerhub-username/my-images:1.0
```

### 总结

通过以上步骤，您已经成功构建了一个应用容器镜像，并可以根据需要定制和扩展这个镜像。
