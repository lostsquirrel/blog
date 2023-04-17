---
title: "Easeprobe 使用实验 8.4: Navtive Client MySQL"
date: 2023-04-14T14:48:23+08:00
tags:
- easeprobe
- native
- mysql
- config
categories:
- lab
draft: false

---
## 实验目的

1. 创建 easeprobe native client mysql probe 配置并运行
2. 执行内容
    - case 1: 验证 默认
    - case 2: 验证 key, value 均一致
    - case 3: 验证 key 一致， value 与设置不一致
    - case 4: 验证 key 不存在

## 准备工作

```sh
mysql -udemo -h 127.0.0.1 -p < /pre/db.sql
```

建表语句和测试数据
```sql
CREATE TABLE `demo`.`product`  (
  `id` int NOT NULL AUTO_INCREMENT,
  `name` varchar(255) NOT NULL,
  PRIMARY KEY (`id`)
);

INSERT INTO `demo`.`product` (`name`) VALUES ("EaseProbe");

CREATE TABLE `demo`.`employee`  (
  `id` int NOT NULL AUTO_INCREMENT,
  `name` varchar(255) NOT NULL,
  `age` int NOT NULL,
  PRIMARY KEY (`id`)
);

INSERT INTO `demo`.`employee` (name, age) VALUES ("Xin", 39);
INSERT INTO `demo`.`employee` (name, age) VALUES ("Hao", 45);
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
    network_mode: container:mysql
    depends_on:
      - mysql
  mysql:
    image: mysql:8.0
    container_name: mysql
    environment:
      MYSQL_RANDOM_ROOT_PASSWORD: yes
      MYSQL_DATABASE: demo
      MYSQL_USER: demo
      MYSQL_PASSWORD: secpass
    volumes:
      - type: bind
        source: ./db.sql
        target: /pre/db.sql
        read_only: true
    ports:
      - 8181:8181

```

## easeprobe 配置 `config.yaml`

```yaml
client:
  - name: MySQL Native Client (local) case 1
    driver: "mysql"
    host: "localhost:3306"
    username: "demo"
    password: "secpass"
    timeout: 5s
  - name: MySQL Native Client (local) case 2
    driver: "mysql"
    host: "localhost:3306"
    username: "demo"
    password: "secpass"
    timeout: 5s
    data:        
      "demo:product:name:id:1" : "EaseProbe"
      "demo:employee:age:id:2" : 45
  - name: MySQL Native Client (local) case 3
    driver: "mysql"
    host: "localhost:3306"
    username: "demo"
    password: "secpass"
    timeout: 5s
    data:         
      "demo:product:name:id:1" : "NameNotExists"
  - name: MySQL Native Client (local) case 4
    driver: "mysql"
    host: "localhost:3306"
    username: "demo"
    password: "secpass"
    timeout: 5s
    data:         
      "demo:product:sallary:id:1" : "NameNotExists"
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

- [配置文件](https://gist.github.com/084771c373e87c6476f535d7c48c9cf5.git)
- [实验环境]({{< ref "/posts/docker-playground">}} "实验环境")
## 验证

- 查看日志
- 查看 8181 端口

## 视频

{{< youtube tOnVn-ZGbLs >}}

## 问题

1. 尝试使用非 admin 用户，显示认证失败