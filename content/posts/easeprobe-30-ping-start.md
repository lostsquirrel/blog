---
title: "Easeprobe 使用实验 3.0: 简单 ping probe"
date: 2023-03-01T17:12:40+08:00
tags:
- easeprobe
- tcp
categories:
- lab
draft: false

---
## 实验目的

1. 创建 easeprobe ping probe 配置并运行

2. 探测 `127.0.0.1`

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
    
    network_mode: host
```
## easeprobe 配置 `config.yaml`

```yaml
ping:
  - name: local
    host: 127.0.0.1
    count: 5
    lost: 0.2
    privileged: true
notify:
  log:
    - name: notify log file # local log file
      file: /var/log/notify.log
log:
  file: /var/log/probe.log
  level: "debug"
```

## 环境

- [配置文件](https://gist.github.com/b6993ae1dd9c732e6b3bef9d533ee664.git)
- [实验环境]({{< ref "/posts/docker-playground">}} "实验环境")

## 验证

- 查看日志
- 查看 8181 端口

## 视频

{{< youtube rKQKv58DXDI >}}

## 问题

`docker-compose.yaml` 中 `network_mode: host` 时，
`config.yaml` 只有设置 `privileged: true` 时才能正常工件

如果 `docker-compose.yaml` 中没有 `network_mode: host` `config.yaml` 配置同官网描述一致。