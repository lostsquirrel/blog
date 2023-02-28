---
title: "EaseProbe 使用实验 3.1: 简单 ping probe"
date: 2023-02-28T15:55:06+08:00
tags:
- easeprobe
- tcp
categories:
- lab
draft: false

---
{{<gist lostsquirrel 21a41137eceac98c057ae5dee335e11b "easeprobe-31-ping-udp.md" >}}

## docker 配置
{{<gist lostsquirrel 21a41137eceac98c057ae5dee335e11b "docker-compose.yaml" >}}

## easeprobe 配置
{{<gist lostsquirrel 21a41137eceac98c057ae5dee335e11b "config.yaml" >}}

## 验证

- 查看日志
- 查看 8181 端口

## 问题

当 config.yaml 中配置 `privileged: false`

不管容器设置配置 privileged: true,都会出错

错误信息： `Error (ping): socket: permission denied`