---
title: "EaseProbe 使用实验 1.1: 简单 http probe"
date: 2023-02-24T18:18:02+08:00
tags:
- easeprobe
- http
categories:
- lab
draft: false

---

## 实验目的

1. 用最简单的配置将 easeprobe 的 http probe 跑起来

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
```

## easeprobe 配置 `config.yaml`

```yaml
http:
  - name: Google
    url: https://www.google.com

notify:
  log:
    - name: log file # local log file
      file: /dev/stdout
```

## 环境

- 配置文件 https://gist.github.com/5d87509d3ebaf7bea69376d04af6f864.git
- [实验环境]({{< ref "/posts/docker-playground">}} "实验环境")

## 验证

- 查看日志
- 查看 8181 端口

## 视频

- https://youtu.be/0BcRlbrhRyI