---
title: "Easeprobe 使用实验 8.2: Native Client Memcache"
date: 2023-03-28T14:52:26+08:00
tags:
categories:
draft: false

---

## 实验目的

1. 创建 easeprobe native client memcache probe 配置并运行
2. 执行内容
    - case 1: 验证 默认
    - case 2: 验证 key, value 均一致
    - case 3: 验证 key 一致， value 与设置不一致
    - case 4: 验证 key 不存在

## 准备工作

- 安装 telnet 
    `apk add busybox-extras`

- 向 memcache 写入测试数据
```
set key 0 0 3
val
set test:key 0 0 8
test_val
```

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
      - 11211:11211
  memcache:
    image: memcached:1.6
    container_name: memcache
    network_mode: container:probe
```

## easeprobe 配置 `config.yaml`

```yaml
client:
  - name: Memcache Native Client (local) case 1
    driver: "memcache"
    host: "localhost:11211"
    timeout: 5s
  - name: Memcache Native Client (local) case 2
    driver: "memcache"
    host: "localhost:11211"
    timeout: 5s
    data:        
      key: val  
      "test:key": test_val
  - name: Memcache Native Client (local) case 3
    driver: "memcache"
    host: "localhost:11211"
    timeout: 5s
    data:         
      key1: val2   
  - name: Memcache Native Client (local) case 4
    driver: "memcache"
    host: "localhost:11211"
    timeout: 5s
    data:         
      key2: val2   

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

- [配置文件](https://gist.github.com/5db6b869686ff3bd6680ff50e2bab14f.git)
- [实验环境]({{< ref "/posts/docker-playground">}} "实验环境")

## 验证

- 查看日志
- 查看 8181 端口

- 由于实验环境为共享容器，物理机负载波动较大，实际表现差距可能很大
## 视频

{{< youtube hIIoHlIQt7g >}}

## 问题