---
title: "Easeprobe 使用实验 5.1: shell 命令的 ssh 配置"
date: 2023-03-03T17:25:41+08:00
tags:
- easeprobe
- http
- shell
- config
categories:
- lab
draft: true

---




{{<gist lostsquirrel 91941b20d40841f896068f0a69dd3a28 "easeprobe-51-shell-cfg.md" >}}

## docker 配置
{{<gist lostsquirrel 91941b20d40841f896068f0a69dd3a28 "docker-compose.yaml" >}}

## easeprobe 配置
{{<gist lostsquirrel 91941b20d40841f896068f0a69dd3a28 "config.yaml" >}}

## 环境

- [实验环境]({{< ref "/posts/docker-playground">}} "实验环境")

## 验证

- 查看日志
- 查看 8181 端口