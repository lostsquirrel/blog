---
title: "Easeprobe 使用实验 8.3: Navtive Client MongoDB"
date: 2023-04-10T14:48:23+08:00
tags:
- easeprobe
- native
- mongodb
- config
categories:
- lab
draft: false

---
## 实验目的

1. 创建 easeprobe native client mongodb probe 配置并运行
2. 执行内容
    - case 1: 验证 默认
    - case 2: 验证 key, value 均一致
    - case 3: 验证 key 一致， value 与设置不一致
    - case 4: 验证 key 不存在

## 准备工作

```
mongo -u root -p secpass --authenticationDatabase admin

use test
db.product.insert({name: "EaseProbe"})


db.createUser(
   {
     user: "probe",
     pwd: "secpass2",
     roles: [
        { role: "readWrite", db: "test" }
     ]
   }
)

db.getUsers()

mongo -u probe -p secpass2 test
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
  mongo:
    image: mongo:4.4.19
    container_name: mongo
    network_mode: container:probe
    depends_on:
      - probe
    environment:
      MONGO_INITDB_ROOT_USERNAME: root
      MONGO_INITDB_ROOT_PASSWORD: secpass
```

## easeprobe 配置 `config.yaml`

```yaml
client:
  - name: MongoDB Native Client (local) case 1
    driver: "mongo"
    host: "localhost:27017"
    username: "root"
    password: "secpass"
    timeout: 5s
  - name: MongoDB Native Client (local) case 2
    driver: "mongo"
    host: "localhost:27017"
    username: "root"
    password: "secpass"
    timeout: 5s
    data:        
      "test:product" : '{"name":"EaseProbe"}'
  - name: MongoDB Native Client (local) case 3
    driver: "mongo"
    host: "localhost:27017"
    username: "root"
    password: "secpass"
    timeout: 5s
    data:         
      "test:product" : '{"name":"EaseProbeValueNotExists"}'
  - name: MongoDB Native Client (local) case 4
    driver: "mongo"
    host: "localhost:27017"
    username: "root"
    password: "secpass"
    timeout: 5s
    data:         
      "test:product" : '{"name_not_exists":"EaseProbeValueNotExists"}'

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

## 实验环境

- [配置文件](https://gist.github.com/4dd10454c05db85086c8986bd4dfafe7.git)
- [实验环境]({{< ref "/posts/docker-playground">}} "实验环境")

## 验证

- 查看日志
- 查看 8181 端口

## 视频

{{< youtube hIIoHlIQt7g >}}

## 问题

1. 尝试使用非 admin 用户，显示认证失败