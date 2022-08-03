---
title: "目录结构说明"
date: 2022-08-02T11:18:05+08:00
draft: false
categories: 
    - hugo
    - tool
    - doc
tags: 
    - tool
    - doc
---

## 目录结构

```
.
├── archetypes
├── assets
├── config.toml
├── content
├── data
├── layouts
├── static
└── themes
```

## 目录说明

### archetypes

通过 `hugo new` 命令创建新文件提使用的模板

### assets

默认不创建， 这个目录中的所有文件都需要被 Hugo Pipes 处理。 `.Permalink` 或 `.RelPermalink` 使用到的文件会被输出到 `public` 目录


### config

hugo 提供了大量的配置指令，config 目录中可以存放以 JSON,YAML或TOML 格式存放的配置指令。最小配置或不需要使用环境配置的就是在根目录下使用一个 `config.toml` 配置


### content

所有内容文件，如果有子级则在该目录创建子目录

### data

存放在生成网站时用到的配置文件， 格式可以是 JSON,YAML或TOML， 也可以通过 数据模板 获取动态内容


### layouts

以 .html 形式存放的网站布局文件


### static

存放 图片，CSS，JavaScript 脚本，在构建网站进会将其中内容原样输出到 `static` 目录 hugo 0.31+ 可以用多个静态目录


### themes

包含模板，布局等成套网站外观解决方案
