---
title: "Docker 入门示例: 镜像"
date: 2024-03-12T14:17:50+08:00
summary: docker 镜像介结及相关操作说明
tags:
  - docker
  - get_start
categories:
  - ops
draft: false
---

## Listing images on the host 列举主机上的镜像

- 查看当前所有镜像

```sh
$ docker images

REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              14.04               1d073211c498        3 days ago          187.9 MB
busybox             latest              2c5ac3f849df        5 days ago          1.113 MB
training/webapp     latest              54bb4e8718e8        5 months ago        348.7 MB
```

- 使用镜像的不同`TAG`，启动不同容器

```sh
docker run -t -i ubuntu:14.04 /bin/bash
docker run -t -i ubuntu:12.04 /bin/bash
docker run -t -i ubuntu /bin/bash
```

如果不使用`TAG`默认使用`latest` 如 `ubuntu:latest`

## Getting a new image 从远程(镜像仓库)拉取新的镜像

```sh
$ docker pull centos
Using default tag: latest
Pulling repository centos
b7de3133ff98: Pulling dependent layers
5cc9e91966f7: Pulling fs layer
511136ea3c5a: Download complete
ef52fb1fe610: Download complete
. . .

Status: Downloaded newer image for centos

# 交互式启动容器
$ docker run -t -i centos /bin/bash

bash-4.1#
```

## Finding images 查找镜像

- web 查找
  [Docker Hub](https://hub.docker.com/)

- 命令查找

```sh
$ docker search sinatra
NAME                                   DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
training/sinatra                       Sinatra training image                          0                    [OK]
marceldegraaf/sinatra                  Sinatra test app                                0
mattwarren/docker-sinatra-demo                                                         0                    [OK]
luisbebop/docker-sinatra-hello-world                                                   0                    [OK]
bmorearty/handson-sinatra              handson-ruby + Sinatra for Hands on with D...   0
subwiz/sinatra                                                                         0
bmorearty/sinatra                                                                      0
. . .
```

## Pulling our image 从远程拉取镜像

```sh
$ docker pull training/sinatra

$ docker run -t -i training/sinatra /bin/bash

root@a8cb6ce02d85:/#
```

## Creating our own images 创建镜像

1. 从镜像启动一个容器修改容器，提交为另一个镜像

```sh
docker run -t -i training/sinatra /bin/bash
```

容器内命令

```sh
root@0b2616b0e5a8:/#

root@0b2616b0e5a8:/# apt-get install -y ruby2.0-dev

root@0b2616b0e5a8:/# gem2.0 install json

$ docker commit -m "Added json gem" -a "Kate Smith" \
0b2616b0e5a8 ouruser/sinatra:v2
```

- 查看结果

```sh

$ docker images

REPOSITORY          TAG     IMAGE ID       CREATED       SIZE
training/sinatra    latest  5bc342fa0b91   10 hours ago  446.7 MB
ouruser/sinatra     v2      3c59e02ddd1a   10 hours ago  446.7 MB
ouruser/sinatra     latest  5db5f8471261   10 hours ago  446.7 MB

```

```sh
$ docker run -t -i ouruser/sinatra:v2 /bin/bash

root@78e82f680994:/#

```

2. 用`Dockerfile`构建镜像

```sh
mkdir sinatra

cd sinatra

touch Dockerfile
```

Dockerfile 文件内容

```dockerfile
# This is a comment
FROM ubuntu:14.04
MAINTAINER Kate Smith <ksmith@example.com>
RUN apt-get update && apt-get install -y ruby ruby-dev
RUN gem install sinatra
```

Dockerfile 命令格式

```dockerfile
INSTRUCTION statement
#　注释

```

从 Dockerfile 构建镜像

```sh
$ docker build -t ouruser/sinatra:v2 .
$ docker run -t -i ouruser/sinatra:v2 /bin/bash

root@8196968dac35:/#
```

## Setting tags on an image 为镜像添加 TAG

```sh
$ docker tag 5db5f8471261 ouruser/sinatra:devel

$ docker images ouruser/sinatra

REPOSITORY          TAG     IMAGE ID      CREATED        SIZE
ouruser/sinatra     latest  5db5f8471261  11 hours ago   446.7 MB
ouruser/sinatra     devel   5db5f8471261  11 hours ago   446.7 MB
ouruser/sinatra     v2      5db5f8471261  11 hours ago   446.7 MB
```

## Image Digests 镜像摘要

```sh
$ docker images --digests | head

REPOSITORY        TAG      DIGEST                                                                     IMAGE ID      CREATED       SIZE
ouruser/sinatra   latest   sha256:cbbf2f9a99b47fc460d422812b6a5adff7dfee951d8fa2e4a98caa0382cfbdbf    5db5f8471261  11 hours ago  446.7 MB

# 2.0 registry
$ docker pull ouruser/sinatra@sha256:cbbf2f9a99b47fc460d422812b6a5adff7dfee951d8fa2e4a98caa0382cfbdbf
```

## Push an image to Docker Hub 推送到远程镜像仓库

```sh
$ docker push ouruser/sinatra

The push refers to a repository [ouruser/sinatra] (len: 1)
Sending image list
Pushing repository ouruser/sinatra (3 tags)
```

后续介绍推送到私有仓库

## Remove an image from the host 删除本地镜像

需要没基于此镜像的容器才能删除
也可以加 `-f` 参数连同容器一起删除

```sh
$ docker rmi training/sinatra

Untagged: training/sinatra:latest
Deleted: 5bc342fa0b91cabf65246837015197eecfa24b2213ed6a51a8974ae250fedd8d
Deleted: ed0fffdcdae5eb2c3a55549857a8be7fc8bc4241fb19ad714364cbfd7a56b22f
Deleted: 5c58979d73ae448df5af1d8142436d81116187a7633082650549c52c3a2418f0
```

## 查看镜像依赖层级关系

```sh
docker inspect --format='{{.Id}} {{.Parent}}' $(docker images --filter since=f50f9524513f -q)
docker inspect --format='{{.Id}} {{.Parent}}' $(docker images --filter since=bf85b4b89889 -q)
docker inspect --format='{{.RepoDigests}} {{.Parent}}' $(docker images --filter since=bf85b4b89889 -q)

docker images --filter since=9c7cc63dd540
docker images --filter since=9c7cc63dd540 -q
```
