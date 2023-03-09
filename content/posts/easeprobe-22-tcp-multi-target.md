---
title: "Easeprobe 使用实验 2.2: tcp probe 多节点，错误节点"
date: 2023-02-27T18:06:37+08:00
tags:
- easeprobe
- tcp
categories:
- lab
draft: false

---

## 实验目的

1. 创建 easeprobe tcp probe 配置并运行
2. probe 探测本机22端口
3. 探测 vultr us 测速节点 443 端口
    - Seattle, Washington wa-us-ping.vultr.com
    - Silicon Valley, California lax-ca-us-ping.vultr.com
    - Los Angeles, California sjo-ca-us-ping.vultr.com
    - Chicago, Illinois	              il-us-ping.vultr.com
    - Dallas, Texas	                  tx-us-ping.vultr.com
    - New York / New Jersey	          nj-us-ping.vultr.com
    - Atlanta, Georgia	              ga-us-ping.vultr.com
    - Miami, Florida	              fl-us-ping.vultr.com
4. 探测一个不存在的节点的 22 端口

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
  - name: local
    host: localhost:22
  - name: Seattle, Washington 
    host: wa-us-ping.vultr.com:443
  - name: Silicon Valley, California
    host: lax-ca-us-ping.vultr.com:443
  - name: Los Angeles, California
    host: sjo-ca-us-ping.vultr.com:443
  - name: Chicago, Illinois
    host: il-us-ping.vultr.com:443
  - name: Dallas, Texas
    host: tx-us-ping.vultr.com:443
  - name: New York / New Jersey
    host: nj-us-ping.vultr.com:443
  - name: Atlanta, Georgia
    host: ga-us-ping.vultr.com:443
  - name: Miami, Florida
    host: fl-us-ping.vultr.com:443
  - name: NeverExisted
    host: never_existed:22

notify:
  log:
    - name: log file # local log file
      file: /dev/stdout
```


## 环境

- [配置文件](https://gist.github.com/bcf5332d8747eda8288865561d591918.git)
- [实验环境]({{< ref "/posts/docker-playground">}} "实验环境")

## 验证

- 查看日志
- 查看 8181 端口

## 视频

{{< youtube zfsO9_d4DHQ >}}
