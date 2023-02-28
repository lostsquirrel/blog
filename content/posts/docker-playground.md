---
title: "Docker 在线实验环境"
date: 2023-02-23T18:20:20+08:00
tags:
    - docker
categories:
- lab
draft: false

---

最近想做一些工具实现需要用到一个实验环境，为了方便读者所以选择一个在线的实验环境。
Google `云实验室` 第一个结果是 [腾讯云实验室](https://cloud.tencent.com/lab)，有一个叫[Docker 快速入门](https://cloud.tencent.com/lab/lab/console/768138035069433) 的实验课程，有名额限制，时间一个小时，并提供不能作为其它用途，遂放弃。

换关键字 `docker playground` 找到 [Docker Playground](https://labs.play-with-docker.com/) 想起这个好久没用的环境了。
- 可以添加多个机器
- 单次使用时长 4 小时
- 1 核 4G(能看到宿主机的8核32G)，4G 10G 
- 可以有端口访问
- 可以通过 `ssh` 访问

所以选择作为后续实验环境

## [Docker Playground](https://labs.play-with-docker.com/) ssh 访问配置
1. `ADD NEW INSTANCE` 添加节点
2. 点击节点在终端查看 密钥

```
cat ~/.ssh/id_rsa
```

3. 将密钥保存到本地
```
tee ~/.ssh/docker_play <<EOF
-----BEGIN OPENSSH PRIVATE KEY-----

-----END OPENSSH PRIVATE KEY-----
EOF

chmod 600 ~/.ssh/docker_play
```
4. 配置密钥

```
tee ~/.ssh/docker_play_config <<EOF
Host dp
  HostName direct.labs.play-with-docker.com
  Port 22
  User ip172-18-0-182-xxx
  IdentityFile ~/.ssh/docker_play
EOF


```

在 `~/.ssh/config` 引入 `~/.ssh/docker_play_config`

## 系统信息

1. 系统版本

```
Alpine Linux v3.16
```

2. `docker` 版本

```
Client:
 Version:           20.10.17
 API version:       1.41
 Go version:        go1.17.11
 Git commit:        100c701
 Built:             Mon Jun  6 22:56:42 2022
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.17
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.17.11
  Git commit:       a89b842
  Built:            Mon Jun  6 23:01:45 2022
  OS/Arch:          linux/amd64
  Experimental:     true
 containerd:
  Version:          v1.6.6
  GitCommit:        10c12954828e7c7c9b6e0ea9b0c02b01407d3ae1
 runc:
  Version:          1.1.2
  GitCommit:        v1.1.2-0-ga916309f
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```
3. `docker-compose` 版本

```
Docker Compose version v2.6.1
```