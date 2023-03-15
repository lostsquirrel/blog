---
title: "Easeprobe 使用实验 7.1: host probe 配置"
date: 2023-03-10T16:46:05+08:00
tags:
- easeprobe
- host
- config
categories:
- lab
draft: false

---
## 实验目的

1. 创建 easeprobe host probe 配置并运行
2. 执行内容
    在节点 cpu mem disk 负载等达到阈值时发送通知

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
        source: /root/.ssh/id_rsa
        target: /pki/id_rsa
        read_only: true
    ports:
      - 8181:8181
    networks:
      - public
  demo:
    image: linuxserver/openssh-server:latest
    container_name: demo
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - USER_NAME=ubuntu
      - PUBLIC_KEY_FILE=/pki/id_rsa.pub
    volumes:
      - type: bind
        source: /root/.ssh/id_rsa.pub
        target: /pki/id_rsa.pub
        read_only: true
    networks:
      - public
networks:
  public:
    ipam:
      driver: default
      config:
      - subnet: 172.28.0.0/24

```

## easeprobe 配置 `config.yaml`

```yaml
host:
  servers:
    - name: playground
      host: ubuntu@demo:2222
      key: /pki/id_rsa
      threshold:
        cpu: 1
        load:
          m1: 33
          m5: 32
          m15: 30
    - name: playground-disk-004
      host: ubuntu@demo:2222
      key: /pki/id_rsa
      threshold:
        disk: 0.04
        cpu: 1
        load:
          m1: 33
          m5: 32
          m15: 30
    - name: playground-cpu-08
      host: ubuntu@demo:2222
      key: /pki/id_rsa
      threshold:
        cpu: 0.8
        load:
          m1: 33
          m5: 32
          m15: 30
    - name: playground-mem-05
      host: ubuntu@demo:2222
      key: /pki/id_rsa
      threshold:
        mem: 0.5
        cpu: 1
        load:
          m1: 33
          m5: 32
          m15: 30
    - name: playground-load-20
      host: ubuntu@demo:2222
      key: /pki/id_rsa
      threshold:
        cpu: 1
        load:
          m1: 20
          m5: 20
          m15: 20

notify:
  log:
    - name: notify
      file: /var/log/notify.log

settings:
  http:
    log:
      file: /var/log/access.log
  log:
    level: "debug"

```

## 环境

- [配置文件](https://gist.github.com/d0129d274d695c7e4d2db3f68fa1238c.git)
- [实验环境]({{< ref "/posts/docker-playground">}} "实验环境")

## 验证

- 查看日志
- 查看 8181 端口

- 由于实验环境为共享容器，物理机负载波动较大，实际表现差距可能很大
- apk add stress-ng
- stress-ng -c 1 --vm 15 --timeout 60 --verbose
- dd if=/dev/zero of=1000m.bin bs=1000MB count=1
## 视频

{{< youtube OHL85fCMWFI >}}

## 问题

- 由于环境所在的机器 `top` 命令与常规不同，所以监测目标为容器
- `apk add procps` 安装的 `top` 输出也不符合 `probe` 的要求
