---
title: "Easeprobe 使用实验 8.1: Native Client Redis"
date: 2023-03-16T15:30:38+08:00
tags:
- easeprobe
- native
- redis
- config
categories:
- lab
draft: false

---
## 实验目的

1. 创建 easeprobe native client redis probe 配置并运行
2. 执行内容
    - 检测键 `name` 的值是 `john`

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
    ports:
      - 8181:8181
  redis:
    image: redis:7
    container_name: redis
    hostname: redis.somewhere
    command: --requirepass "secpass"
```

## easeprobe 配置 `config.yaml`

```yaml
client:
  - name: Redis Native Client (somewhere)
    driver: "redis"  
    host: "redis.somewhere:6379"
    password: "secpass" 
    data: 
      name: john 
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
    file: /var/log/debug.log
```

## 环境

- set name john
- [配置文件](https://gist.github.com/2c7bef49fec94f0185f750217ccb18de.git)
- [实验环境]({{< ref "/posts/docker-playground">}} "实验环境")

## 验证

- 查看日志
- 查看 8181 端口

## 视频

{{< youtube _quAZrtrcKQ >}}

## 问题