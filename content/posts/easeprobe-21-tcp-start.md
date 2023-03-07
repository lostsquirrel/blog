---
title: "EaseProbe 使用实验 2.1: 简单 tcp probe"
date: 2023-02-27T16:45:01+08:00
tags:
- easeprobe
- tcp
categories:
- lab
draft: false

---

## 实验目的

1. 创建 easeprobe tcp probe 配置并运行
2. probe 探测服务为本机

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
tcp:
  - name: host
    host: localhost:22

notify:
  log:
    - name: log file # local log file
      file: /dev/stdout
```

## 环境

- [配置文件](https://gist.github.com/8ba26ab0cbbcbbfd27ce4b8198841cfb.git)
- [实验环境]({{< ref "/posts/docker-playground">}} "实验环境")

## 验证

- 查看日志
- 查看 8181 端口