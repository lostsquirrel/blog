---
title: "Easeprobe 使用实验 5.1: shell 命令的 ssh 配置"
date: 2023-03-03T17:25:41+08:00
tags:
- easeprobe
- http
- shell
- config
categories:
- lab
draft: false

---


## 实验目的

1. easeprobe shell command probe 的 ssh 配置使用
2. ssh 配合堡垒机的使用

## 实验准备

1. 使用 ssh-keygen 为堡垒机生成两组密钥对,为服务器生成3组,最后一个用密码

使用 `ssh-keygen -t rsa -b 4096` 生成

- 第一对名称为 `aws_rsa` `aws_rsa.pub`, 密码为 `secpass`
- 第二对名称为 `gcp_rsa`  `gcp_rsa.pub`, 密码为空
- 第三对名称为 `a_rsa`  `a_rsa.pub`, 密码为空
- 第四对名称为 `b_rsa` `b_rsa.pub`, 密码为 `secpass2`
- 第五对名称为 `c_rsa` `c_rsa.pub`, 密码为空

## 节点拓扑

    {{<b2img "/easeprobe-51-topology.png" "topology" >}}

## 配置说明

1. 节点 aws-bastion 主机名 aws.basition.com 模拟为 aws 的堡垒机, 同时连接公网和内网
2. 节点 aws-a 主机名 a.server， aws-b 主机名 b.server 模拟为 aws 内部节点，只能在内网访问
    - aws-a 执行简单 hostname 命令
    - aws-b 执行 redis-cli
3. 节点 gcp-bastion 主机名 gcp.basition.com 模拟为 gcp 的堡垒机, 同时连接公网和内网
4. 节点 gcp-c 主机名 c.server， aws-d 主机名 d.server 模拟为 gcp 内部节点，只能在内网访问
    - gcp-c 执行简单 hostname 命令, 并检测一个不存在的返回，验证错误情况
    - gcp-d 执行简单 hostname 命令

## docker-compose 配置

```yaml
version: "3.9"
services:
  probe:
    image: megaease/easeprobe:v2.0.1
    container_name: probe
    volumes:
      - type: bind
        source: ./config.yaml
        target: /opt/config.yaml
        read_only: true
      - type: bind
        source: ./aws_rsa
        target: /pki/aws_rsa
        read_only: true
      - type: bind
        source: ./gcp_rsa
        target: /pki/gcp_rsa
        read_only: true
      - type: bind
        source: ./a_rsa
        target: /pki/a_rsa
        read_only: true
      - type: bind
        source: ./b_rsa
        target: /pki/b_rsa
        read_only: true
      - type: bind
        source: ./c_rsa
        target: /pki/c_rsa
        read_only: true
    ports:
      - 8181:8181
    networks:
      - public
  aws-bastion:
    image: linuxserver/openssh-server:latest
    container_name: aws-bastion
    hostname: aws.basition.com
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - USER_NAME=ubuntu
      - PUBLIC_KEY_FILE=/pki/aws_rsa.pub
      - DOCKER_MODS=linuxserver/mods:openssh-server-ssh-tunnel
    volumes:
      - type: bind
        source: ./aws_rsa.pub
        target: /pki/aws_rsa.pub
        read_only: true
    networks:
      - public
      - aws
  gcp-bastion:
    image: linuxserver/openssh-server:latest
    container_name: gcp-bastion
    hostname: gcp.basition.com
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - USER_NAME=ubuntu
      - PUBLIC_KEY_FILE=/pki/gcp_rsa.pub
      - DOCKER_MODS=linuxserver/mods:openssh-server-ssh-tunnel
    volumes:
      - type: bind
        source: ./gcp_rsa.pub
        target: /pki/gcp_rsa.pub
        read_only: true
    networks:
      - public
      - gcp
  aws-a:
    image: linuxserver/openssh-server:latest
    container_name: aws-a
    hostname: a.server
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - USER_NAME=ubuntu
      - PUBLIC_KEY_FILE=/pki/a_rsa.pub
      - DOCKER_MODS=linuxserver/mods:openssh-server-ssh-tunnel
    volumes:
      - type: bind
        source: ./a_rsa.pub
        target: /pki/a_rsa.pub
        read_only: true
    networks:
      - aws
  aws-b:
    build: .
    image: openssh-server-with-redis-cli:latest
    container_name: aws-b
    hostname: b.server
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - USER_NAME=ubuntu
      - PUBLIC_KEY_FILE=/pki/b_rsa.pub
      - DOCKER_MODS=linuxserver/mods:openssh-server-ssh-tunnel
    volumes:
      - type: bind
        source: ./b_rsa.pub
        target: /pki/b_rsa.pub
        read_only: true
    networks:
      - aws
  aws-b-redis:
    image: redis:7
    container_name: b-redis
    command: --requirepass "secpass"
    network_mode: service:aws-b
  gcp-c:
    image: linuxserver/openssh-server:latest
    container_name: gcp-c
    hostname: c.server
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - USER_NAME=ubuntu
      - PUBLIC_KEY_FILE=/pki/c_rsa.pub
      - DOCKER_MODS=linuxserver/mods:openssh-server-ssh-tunnel
    volumes:
      - type: bind
        source: ./c_rsa.pub
        target: /pki/c_rsa.pub
        read_only: true
    networks:
      - gcp
  gcp-d:
    image: linuxserver/openssh-server:latest
    container_name: gcp-d
    hostname: d.server
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - USER_NAME=ubuntu
      - PASSWORD_ACCESS=true
      - USER_PASSWORD=secpass
      - DOCKER_MODS=linuxserver/mods:openssh-server-ssh-tunnel
    networks:
      - gcp
networks:
  public:
  aws:
  gcp:
```

## easeprobe 配置 `config.yaml`

```yaml
ssh:
  bastion:
    aws:
      host: aws.basition.com:2222
      username: ubuntu
      key: /pki/aws_rsa
      passphrase: secpass
    gcp:
      host: ubuntu@gcp.basition.com:2222
      key: /pki/gcp_rsa
  servers:
    - name: A (AWS)
      bastion: aws
      host: a.server:2222
      username: ubuntu
      key: /pki/a_rsa
      cmd: "hostname"
      args:
        - "$A"
      env:
        - "A=aaa"
      contain : "a"
      not_contain: "bbb"
      regex: false
    - name: B (AWS)
      bastion: aws
      host: b.server:2222
      username: ubuntu
      key: /pki/b_rsa
      passphrase: secpass2
      cmd: "/usr/bin/redis-cli"
      args:
        - "-h"
        - "127.0.0.1"
        - "ping"
      env:
        - "REDISCLI_AUTH=secpass"
      contain : "PONG"
      not_contain: "failure"
      regex: false
    - name: C (GCP)
      bastion: gcp
      host: c.server:2222
      username: ubuntu
      key: /pki/c_rsa
      cmd: "hostname"
      args:
        - "$C"
      env:
        - "C=eee"
      contain : "c"
      not_contain: "e"
      regex: false
    - name: D (GCP)
      bastion: gcp
      host: d.server:2222
      username: ubuntu
      password: secpass
      cmd: "hostname"
      args:
        - "$D"
      env:
        - "D=ddd"
      contain : "d"
      not_contain: "x"
      regex: false

notify:
  log:
    - name: notify log file # local log file
      file: /var/log/notify.log
```

## 环境

- [配置文件](https://gist.github.com/a72df724950247222132d0e8e1b17756.git)
- [实验环境]({{< ref "/posts/docker-playground">}} "实验环境")

## 验证

- 查看日志
- 查看 8181 端口

## 视频

<!-- {{< youtube Ibkr6vS0CGE >}} -->

## 问题

- 使用 docker 容器模拟会有些问题
