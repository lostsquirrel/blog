---
title: "Docker 入门示例 hello word"
date: 2024-03-12T11:02:08+08:00
summary: Docker 简单使用示例
tags:
  - docker
  - get_start
categories:
  - ops
draft: false
---

## Run a Hello world 第一个示例

```sh
$ docker run ubuntu /bin/echo 'Hello world'

Hello world
```

- `docker run` runs a container. 启动一个新的容器
- `ubuntu` is the image you run, for example the Ubuntu operating system image（ubuntu:latest）. 容器的基础镜像
  When you specify an image, Docker looks first for the image on your Docker host.
  If the image does not exist locally, then the image is pulled from the public image registry Docker Hub.
  指定镜像首先在本地查找，如果找到则使用本地镜像启动，如果没有则从远程拉取后再启动

- `/bin/echo` is the command to run inside the new container. 在容器中执行的命令
  容器输出： Hello world

Docker containers only run as long as the command you specify is active
容器只在指定命令运行时运行

## Run an interactive container 交互式启动容器

```sh
$ docker run -t -i ubuntu /bin/bash

root@af8bae53bdd3:/#
```

- `docker run` runs a container.

- `ubuntu` is the image you would like to run.

- `-t` flag assigns a pseudo-tty or terminal inside the new container. 启动一个容器内的终端

- `-i` flag allows you to make an interactive connection by grabbing the standard in (STDIN) of the container. 与容器的标准输入建立链接

- `/bin/bash` launches a Bash shell inside our container. 启动容器内的 bash shell

### 在容器中执行命令

```sh
root@af8bae53bdd3:/# pwd

/

root@af8bae53bdd3:/# ls

bin boot dev etc home lib lib64 media mnt opt proc root run sbin srv sys tmp usr var

root@af8bae53bdd3:/# exit
```

## Start a daemonized Hello world 启动一个持续运行的应用

```sh
$ docker run -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"

1e5535038e285177d5214659a068137486f96ee5c2e85a4ac52dc83f2ebe4147
```

- `-d` flag runs the container in the background (to daemonize it). 后台运行

### 查看当前运行的容器

```sh
$ docker ps

CONTAINER ID  IMAGE         COMMAND               CREATED        STATUS       PORTS NAMES
1e5535038e28  ubuntu  /bin/sh -c 'while tr  2 minutes ago  Up 1 minute        insane_babbage
```

### 查看容器运行日志

```sh
$ docker logs insane_babbage

hello world
hello world
hello world
. . .
```

### 停止容器

```sh
$ docker stop insane_babbage

insane_babbage
# 再次查看当前运行的容器
$ docker ps

CONTAINER ID  IMAGE         COMMAND               CREATED        STATUS       PORTS NAMES
```

## 说明

- 整理自官方教程
