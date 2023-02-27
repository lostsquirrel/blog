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
---


{{<gist lostsquirrel 8ba26ab0cbbcbbfd27ce4b8198841cfb "easeprobe-tcp-21.md" >}}

## docker 配置
{{<gist lostsquirrel 8ba26ab0cbbcbbfd27ce4b8198841cfb "docker-compose.yaml" >}}

## easeprobe 配置
{{<gist lostsquirrel 8ba26ab0cbbcbbfd27ce4b8198841cfb "config.yaml" >}}

## 验证

- 查看日志
- 查看 8181 端口