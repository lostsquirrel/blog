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

{{<gist lostsquirrel 5d87509d3ebaf7bea69376d04af6f864 "easeprobe_http_start.md" >}}

## docker 配置

{{<gist lostsquirrel 5d87509d3ebaf7bea69376d04af6f864 "docker-compose.yaml" >}}

## easeprobe 配置

{{<gist lostsquirrel 5d87509d3ebaf7bea69376d04af6f864 "config.yaml" >}}

## 环境

- [实验环境]({{< ref "/posts/docker-playground">}} "实验环境")

## 验证

- 查看日志
- 查看 8181 端口