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
{{<gist lostsquirrel bcf5332d8747eda8288865561d591918 "easeprobe-tcp-22.md" >}}

## docker 配置

{{<gist lostsquirrel bcf5332d8747eda8288865561d591918 "docker-compose.yaml" >}}

## easeprobe 配置

{{<gist lostsquirrel bcf5332d8747eda8288865561d591918 "config.yaml" >}}


## 环境

- [实验环境]({{< ref "/posts/docker-playground">}} "实验环境")

## 验证

- 查看日志
- 查看 8181 端口