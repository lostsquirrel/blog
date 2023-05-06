---
title: "Easeprobe 使用实验 8.5: Native Client Kafka"
date: 2023-05-04T17:13:57+08:00
tags:
- easeprobe
- native
- kafka
- config
categories:
- lab
draft: false

---

## 实验目的

1. 创建 easeprobe native client kafka probe 配置并运行


## 准备工作

```sh
KAFKA_VERSION=3.3.2
SCALA_VERSION=2.13
yum install -y wget ca-certificates  java-11-openjdk tmux
mirror=$(curl --stderr /dev/null https://www.apache.org/dyn/closer.cgi\?as_json\=1 | jq -r '.preferred')
wget "${mirror}kafka/${KAFKA_VERSION}/kafka_${SCALA_VERSION}-${KAFKA_VERSION}.tgz" 
tar -xzf kafka_${SCALA_VERSION}-${KAFKA_VERSION}.tgz
mv kafka_${SCALA_VERSION}-${KAFKA_VERSION} kafka

tmux new -s zk
KAFKA_HEAP_OPTS="-Xmx128M -Xms128M" bin/zookeeper-server-start.sh config/zookeeper.properties

tmux new -s kafka
KAFKA_OPTS="-Xmx512M -Xms512M" bin/kafka-server-start.sh config/server.properties
```
    
## docker-compose 配置

```yaml
version: "3.3"
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
client:
  - name: Kafka Native Client (local)
    driver: "kafka"
    host: "localhost:9092"
notify:
  log:
    - name: notify
      file: /dev/stdout

settings:
  http:
    log:
      file: /var/log/access.log
  log:
    level: "debug"
    file: /var/log/debug.log
```

## 实验环境

- [配置文件](https://gist.github.com/0c1b1fe7a1dcf4ec8f7322bec1267ba0.git)
- [实验环境]({{< ref "/posts/play-with-k8s">}} "实验环境")

## 验证

- 查看日志
- 查看 8181 端口

## 视频

{{< youtube DzxYuayXS3E >}}
## 问题

    猜测可能是实验环境物理机硬盘用尽，无法拉取太大的镜像，只能手动启动 zk 和 kafka