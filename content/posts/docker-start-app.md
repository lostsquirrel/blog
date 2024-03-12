---
title: "Docker 入门示例: 部署一个简单应用"
date: 2024-03-12T14:05:12+08:00
summary: |
  Docker 命令行客户端使用介结，
  并使用 Docker 部署一个简单的 web 应用
tags:
  - docker
  - get_start
categories:
  - ops
draft: false
---

## Learn about the Docker client 学习客户端使用

```sh
# Usage:  [sudo] docker [subcommand] [flags] [arguments] ..
# Example:
$ docker run -i -t ubuntu /bin/bash
```

- 查看 docker 版本信息

```sh
docker version
```

```sh
Client:
 Version:      1.11.2
 API version:  1.23
 Go version:   go1.5.4
 Git commit:   b9f10c9
 Built:        Wed Jun  1 22:00:43 2016
 OS/Arch:      linux/amd64

Server:
 Version:      1.11.2
 API version:  1.23
 Go version:   go1.5.4
 Git commit:   b9f10c9
 Built:        Wed Jun  1 22:00:43 2016
 OS/Arch:      linux/amd64

```

## Get Docker command help 查看帮助信息

- 查看命令帮助

```sh
docker --help
```

- 查看子命令帮助

```sh
$ docker attach --help

Usage: docker attach [OPTIONS] CONTAINER

Attach to a running container

  --help              Print usage
  --no-stdin          Do not attach stdin
  --sig-proxy=true    Proxy all received signals to the process
```

## Running a web application in Docker 运行一个 web 应用

```sh
docker run -d -P training/webapp python app.py
```

- `-P` flag is new and tells Docker to map any required network ports inside our container to our host
  自动映射端口 默认 32768 to 61000
- `training/webapp` contains a simple Python Flask web application 一个包含 web 应用的镜像

## Viewing our web application container 查看运行的 web 容器

```sh
$ docker ps -l

CONTAINER ID  IMAGE                   COMMAND       CREATED        STATUS        PORTS                    NAMES
bc533791f3f5  training/webapp:latest  python app.py 5 seconds ago  Up 2 seconds  0.0.0.0:49155->5000/tcp  nostalgic_morse
```

- `-l` return the details of the last container started 最后启动的容器的详细信息
- `-a` 包含已经停止的容器

- 指定端口映射

```sh
docker run -d -p 80:5000 training/webapp python app.py
```

## A network port shortcut 查看端口映射

```
$ docker port nostalgic_morse 5000

0.0.0.0:49155（映射的主机端口）
```

## Viewing the web application’s logs 查看web应用日志

```sh
$ docker logs -f nostalgic_morse

* Running on http://0.0.0.0:5000/
10.0.2.2 - - [23/May/2014 20:16:31] "GET / HTTP/1.1" 200 -
10.0.2.2 - - [23/May/2014 20:16:31] "GET /favicon.ico HTTP/1.1" 404 -
```


## Looking at our web application container’s processes 看查web容器进程

```sh
$ docker top nostalgic_morse

PID                 USER                COMMAND
854                 root                python app.py
```

## Inspecting our web application container 检示web容器详细信息

```sh
# 显示全部
$ docker inspect nostalgic_morse

# 指定范围
$ docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' nostalgic_morse
```

## Stopping our web application container 停止容器

```sh
$ docker stop nostalgic_morse

nostalgic_morse

$ docker ps -l
```

## Restarting our web application container 再次启动容器

```sh
$ docker start nostalgic_morse

nostalgic_morse
```

## Removing our web application container 删除容器

```sh
$ docker rm nostalgic_morse

Error: Impossible to remove a running container, please stop it first or use -f
2014/05/24 08:12:56 Error: failed to remove one or more containers

$ docker stop nostalgic_morse

nostalgic_morse

$ docker rm nostalgic_morse

nostalgic_morse

```

说明：
运行中的容器无法直接删除
加 `-f` 参数可强制删除运行中的容器