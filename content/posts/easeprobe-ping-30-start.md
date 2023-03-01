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
{{<gist lostsquirrel b6993ae1dd9c732e6b3bef9d533ee664 "easeprobe-30-ping-local.md" >}}

## docker 配置
{{<gist lostsquirrel b6993ae1dd9c732e6b3bef9d533ee664 "docker-compose.yaml" >}}

## easeprobe 配置
{{<gist lostsquirrel b6993ae1dd9c732e6b3bef9d533ee664 "config.yaml" >}}

## 环境

- [实验环境]({{< ref "/posts/docker-playground">}} "实验环境")

## 验证

- 查看日志
- 查看 8181 端口

## 问题

`docker-compose.yaml` 中 `network_mode: host` 时，
`config.yaml` 只有设置 `privileged: true` 时才能正常工件

如果 `docker-compose.yaml` 中没有 `network_mode: host` `config.yaml` 配置不官网描述一致。