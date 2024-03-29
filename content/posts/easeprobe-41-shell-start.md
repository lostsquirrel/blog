---
title: "Easeprobe 使用实验 4.1: shell probe start"
date: 2023-03-02T17:53:49+08:00
tags:
- easeprobe
- tcp
- shell
categories:
- lab
draft: false

---

## 实验目的

1. 创建 easeprobe shell probe 配置并运行
2. 执行内容
    1. 执行 `test.sh` 内部就是 `printenv` 并打印参数
    2. 执行 `redis-cli` ping 并验证是否返回 PONG
    3. 执行 `zookeeper` 的 `stat` 指令并验证返回状态

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
        source: ./test.sh
        target: /scripts/test.sh
        read_only: true
    ports:
      - 8181:8181
  redis:
    image: redis:7
    container_name: redis
    command: --requirepass "secpass"
  zookeeper:
    image: 'zookeeper:3.6'
    container_name: zookeeper  
    environment:
      ZOO_4LW_COMMANDS_WHITELIST: stat,srvr
```

## easeprobe 配置 `config.yaml`

```yaml
shell:
  - name: test script
    cmd: "/scripts/test.sh"
    args:
      - "socks5://127.0.0.1:1085"
      - "www.google.com"
  - name: Redis
    cmd: "redis-cli"
    args:
      - "-h"
      - "redis"
      - "ping"
    clean_env: true 
    env:
      - "REDISCLI_AUTH=secpass"
    contain : "PONG"
    not_contain: "failure"
    regex: false
  - name: Zookeeper
    cmd: "/bin/sh"
    args:
      - "-c"
      - "echo stat |  zookeeper 2181"
    contain: "Mode:"
notify:
  log:
    - name: notify log file # local log file
      file: /var/log/notify.log
log:
  file: /var/log/probe.log
  level: "debug"
```

## 环境

- [配置文件](https://gist.github.com/91941b20d40841f896068f0a69dd3a28.git)
- [实验环境]({{< ref "/posts/docker-playground">}} "实验环境")

## 验证

- 查看日志
- 查看 8181 端口

## 视频

{{< youtube Ibkr6vS0CGE >}}