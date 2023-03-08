---
title: "EaseProbe 使用实验 3.1: ping probe 多目标"
date: 2023-02-28T15:55:06+08:00
tags:
- easeprobe
- tcp
categories:
- lab
draft: false

---
## 实验目的

1. 创建 easeprobe ping probe 配置并运行

2. 探测 vultr us 测速节点
    - Seattle, Washington wa-us-ping.vultr.com
    - Silicon Valley, California lax-ca-us-ping.vultr.com
    - Los Angeles, California        sjo-ca-us-ping.vultr.com
    - Chicago, Illinois	              il-us-ping.vultr.com
    - Dallas, Texas	                  tx-us-ping.vultr.com
    - New York / New Jersey	          nj-us-ping.vultr.com
    - Atlanta, Georgia	              ga-us-ping.vultr.com
    - Miami, Florida	              fl-us-ping.vultr.com

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
ping:
  - name: Seattle, Washington 
    host: wa-us-ping.vultr.com
    count: 5
    lost: 0.2
    privileged: false
  - name: Silicon Valley, California
    host: lax-ca-us-ping.vultr.com
    count: 5
    lost: 0.2
    privileged: false
  - name: Los Angeles, California
    host: sjo-ca-us-ping.vultr.com
    count: 5
    lost: 0.2
    privileged: false
  - name: Chicago, Illinois
    host: il-us-ping.vultr.com
    count: 5
    lost: 0.2
    privileged: false
  - name: Dallas, Texas
    host: tx-us-ping.vultr.com
    count: 5
    lost: 0.2
    privileged: false
  - name: New York / New Jersey
    host: nj-us-ping.vultr.com
    count: 5
    lost: 0.2
    privileged: false
  - name: Atlanta, Georgia
    host: ga-us-ping.vultr.com
    count: 5
    lost: 0.2
    privileged: false
  - name: Miami, Florida
    host: fl-us-ping.vultr.com
    count: 5
    lost: 0.2
    privileged: false

notify:
  log:
    - name: notify log file # local log file
      file: /var/log/notify.log
log:
  file: /var/log/probe.log
  level: "debug"
```

## 环境

- [配置文件](https://gist.github.com/21a41137eceac98c057ae5dee335e11b.git)
- [实验环境]({{< ref "/posts/docker-playground">}} "实验环境")

## 验证

- 查看日志
- 查看 8181 端口

## 视频

- https://youtu.be/V-WsZEsc3lQ

## 问题

当 config.yaml 中配置 `privileged: false`

不管容器设置配置 privileged: true,都会出错

错误信息： `Error (ping): socket: permission denied`

本地验证正常执行，猜测可能和实验环境是 docker-in-docker 环境的原因